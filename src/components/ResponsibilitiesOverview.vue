<template>
  <div class="responsibilities-directory">
    <div class="directory-header">
      <div class="header-text">
        <h1 class="directory-title">ER Resident Responsibilities</h1>
        <p class="directory-subtitle">{{ intro }}</p>
      </div>
      <span class="revised-tag">Last Revised July 2026</span>
    </div>

    <!-- SELECTOR -->
    <div v-if="!selected" class="selector-grid">
      <button v-for="role in roles" :key="role.id" class="selector-card" @click="selected = role.id">
        <span class="section-eyebrow">{{ role.eyebrow }}</span>
        <h2 class="card-title">{{ role.title }}</h2>
        <span class="role-hours">{{ role.hours }}</span>
      </button>
    </div>

    <!-- DETAIL VIEW -->
    <div v-else class="detail-view">
      <button class="back-btn" @click="selected = null">← Back to shifts</button>

      <section class="card duty-card">
        <span class="section-eyebrow">{{ activeRole.eyebrow }}</span>
        <h2 class="card-title">{{ activeRole.title }}</h2>
        <span class="role-hours role-hours--detail">{{ activeRole.hours }}</span>

        <p class="now-indicator">
          Current time: {{ nowLabel }}
          <span class="now-indicator-hint">— dimmed items are outside their active window right now</span>
        </p>

        <ol class="duty-list">
          <li v-for="(d, i) in activeRole.duties" :key="i" class="duty-row"
            :class="{ 'duty-row--dim': !isDutyActive(d) }">
            <span class="duty-num">{{ i + 1 }}</span>
            <div class="duty-text">
              <span>{{ d.text }}</span>
              <span v-if="d.time" class="duty-time"> &nbsp;{{ d.time }}</span>
              <span v-if="d.note" class="duty-note"> &nbsp;{{ d.note }}</span>
            </div>
          </li>
        </ol>

        <div v-if="activeRole.footnotes" class="footnotes">
          <p v-for="(f, i) in activeRole.footnotes" :key="i">{{ f }}</p>
        </div>
      </section>

      <!-- ER WEEKENDS: WHO INITIATES WHICH STUDY (only shown for AM1 / AM2 weekend roles) -->
      <section v-if="activeRole.initiationTable" class="card table-card">
        <span class="section-eyebrow">ER Weekends</span>
        <h2 class="card-title">{{ activeRole.initiationTable.title }}</h2>
        <p class="table-note">Note: faculty have their own list that they dictate independently.</p>
        <table class="init-table">
          <thead>
            <tr>
              <th>Category</th>
              <th>AGFA List Name</th>
              <th>Who Initiates Dictation</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(row, i) in activeRole.initiationTable.rows" :key="i">
              <td>{{ row.category }}</td>
              <td>{{ row.agfaListName }}</td>
              <td>{{ row.whoInitiates }}</td>
            </tr>
          </tbody>
        </table>
      </section>

<section class="card exceptions-card">
  <span class="section-eyebrow">All Shifts</span>
  <h2 class="card-title">Exceptions — Daytime Assignment OK If</h2>
  <ol class="duty-list">
    <li v-for="(e, i) in exceptions" :key="i" class="duty-row">
      <span class="duty-num">{{ i + 1 }}</span>
      <div class="duty-text" v-html="e"></div>
    </li>
  </ol>
  <div class="footnotes">
    <p>When leaving a study for AM subspecialty interpretation you <strong>MUST</strong> leave a study comment and assign the study to the correct subspecialty.</p>
  </div>
</section>

      <section class="card overread-card">
        <span class="section-eyebrow">All Shifts</span>
        <h2 class="card-title">Department Overread Policy</h2>
        <ol class="duty-list">
          <li v-for="(o, i) in overreadPolicy" :key="i" class="duty-row">
            <span class="duty-num">{{ i + 1 }}</span>
            <div class="duty-text">{{ o }}</div>
          </li>
        </ol>
      </section>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted, onUnmounted } from 'vue'

const selected = ref(null)

const intro = 'Responsibilities include ALL STAT studies with acute indications and routine studies if called by the clinician within reason.'

/**
 * Each duty may optionally include a `window: { start: 'HH:MM', end: 'HH:MM' }`
 * field (24-hour clock, local time) describing when that specific task is
 * "live." Duties without a `window` are treated as always-active (e.g. open
 * ended tasks like "assist night resident" or "if requested") and are never
 * dimmed.
 *
 * Windows that cross midnight (e.g. 18:30 -> 07:00 for night shifts) are
 * handled automatically by isDutyActive().
 */

const roles = [
  {
    id: 'trieve',
    eyebrow: 'Weekday Evening',
    title: 'TRIEVE',
    hours: '4:30 PM – 9:30 PM',
    duties: [
      { text: 'Inpatient CT Chest, Abdomen/Pelvis (incl. angio studies)', time: 'until 6:30 PM', window: { start: '16:30', end: '18:30' } },
      { text: 'ER, Trauma, and Inpatient CT Neuro', time: 'until 6:30 PM', window: { start: '16:30', end: '18:30' } },
      { text: 'MRI Cord Compression Protocol', time: 'until 6:30 PM', window: { start: '16:30', end: '18:30' } },
      { text: 'ICU plain films', time: 'until 9:30 PM', note: '(ALL including non-STAT)', window: { start: '16:30', end: '21:30' } },
      { text: 'Inpatient and Inpatient Pedi plain films', time: 'until 9:30 PM', window: { start: '16:30', end: '21:30' } },
      { text: 'Outside interpretation studies (overreads)', time: 'from 4:30 PM until 6:30 PM', window: { start: '16:30', end: '18:30' } },
      { text: 'STAT MSRH studies', time: 'from 4:30 PM until 6:30 PM', window: { start: '16:30', end: '18:30' } },
    ],
  },
  {
    id: 'triage',
    eyebrow: 'Weekday Evening',
    title: 'TRIAGE',
    hours: '4:30 PM – 6:30 PM',
    duties: [
      { text: 'ER and Trauma CT Chest, Abdomen/Pelvis (incl. angio studies)', time: 'until 6:30 PM', window: { start: '16:30', end: '18:30' } },
      { text: 'ER, Trauma, and Inpatient CT MSK (incl. angio studies)', time: 'until 6:30 PM', window: { start: '16:30', end: '18:30' } },
      { text: 'ER and Trauma Ultrasound', time: 'until 6:30 PM', window: { start: '16:30', end: '18:30' } },
      { text: 'ER and Trauma Radiographs', time: 'until 6:30 PM', window: { start: '16:30', end: '18:30' } },
      { text: 'Emergent Fluoroscopy and Nuclear Medicine Studies', time: 'until 6:30 PM', window: { start: '16:30', end: '18:30' } },
    ],
  },
  {
    id: 'weekend1',
    eyebrow: 'UH ER Weekend',
    title: 'Weekend 1',
    hours: '7:00 AM – 6:30 PM',
    duties: [
      { text: 'ALL Inpatient Pediatric Radiographs', time: 'until last modified 8:00 AM', note: '(AM2 Faculty)', window: { start: '07:00', end: '08:00' } },
      { text: 'ER and Trauma CT Chest, Abdomen/Pelvis (incl. angio studies)', time: 'until 6:30 PM', note: '(AM1 Faculty until 2:30 PM, then ER Evening Faculty until 6:30 PM)', window: { start: '07:00', end: '18:30' } },
      { text: 'ER, Trauma, and Inpatient MSK CTs (incl. angio studies)', time: 'until 6:30 PM', note: '(AM1 Faculty until 2:30 PM, then ER Evening Faculty until 6:30 PM)', window: { start: '07:00', end: '18:30' } },
      { text: 'ER, Trauma, and Inpatient Ultrasound', time: 'until 6:30 PM', note: '(AM1 Faculty until 2:30 PM, then ER Evening Faculty until 6:30 PM)', window: { start: '07:00', end: '18:30' } },
      { text: 'Emergent Fluoro and Nuclear Medicine Studies', time: 'until 6:30 PM', note: '(AM1 Faculty until 2:30 PM, then ER Evening Faculty until 6:30 PM)', window: { start: '07:00', end: '18:30' } },
      { text: 'Outside Read Interpretations', time: 'until 6:30 PM', note: '(AM1 Faculty until 2:30 PM, then ER Evening Faculty until 6:30 PM)', window: { start: '07:00', end: '18:30' } },
      { text: 'Inpatient CT Chest, Abdomen/Pelvis', time: '2:30 PM – 6:30 PM', note: '(ER Evening Faculty)', window: { start: '14:30', end: '18:30' } },
    ],
  },
  {
    id: 'weekend2',
    eyebrow: 'UH ER Weekend',
    title: 'Weekend 2',
    hours: '7:00 AM – 6:30 PM',
    duties: [
      { text: 'STAT Inpatient Pediatric Radiographs', time: 'starts 8:00 AM (last modified) until 6:30 PM', note: '(AM2 Faculty until 2:30 PM, then ER Evening Faculty until 6:30 PM)', window: { start: '08:00', end: '18:30' } },
      { text: 'STAT Inpatient Radiographs', time: 'until last modified 2:30 PM', note: '(AM2 Faculty)', window: { start: '07:00', end: '14:30' } },
      { text: 'ALL ICU Radiographs', time: 'until last modified 2:30 PM', note: '(AM2 Faculty)', window: { start: '07:00', end: '14:30' } },
      { text: 'ER and Trauma Radiographs', time: 'starts 12:00 PM (last modified) until 6:30 PM', note: '(AM2 Faculty until 2:30 PM, then ER Evening Faculty until 6:30 PM)', window: { start: '12:00', end: '18:30' } },
      { text: 'STAT Inpatient CT Chest, Abdomen/Pelvis', time: 'until 2:30 PM', note: '(AM1 Faculty)', window: { start: '07:00', end: '14:30' } },
      { text: 'ER, Trauma, and Inpatient CT Neuro', time: 'starts 2:30 PM until 6:30 PM', note: '(ER Evening Faculty)', window: { start: '14:30', end: '18:30' } },
      { text: 'MRI Cord Compression Protocol', time: 'starts 2:30 PM until 6:30 PM', note: '(ER Evening Faculty)', window: { start: '14:30', end: '18:30' } },
      { text: 'Assist ER Weekend 1', time: 'until 6:30 PM', window: { start: '07:00', end: '18:30' } },
    ],
  },
  {
    id: 'weekend3',
    eyebrow: 'UT ER Weekend',
    title: 'Weekend 3',
    hours: '7:00 AM – 6:30 PM',
    duties: [
      { text: 'MSRH ER and Inpatient CT Chest, Abdomen/Pelvis, and Neuro (incl. angio studies)', time: 'until 6:30 PM', window: { start: '07:00', end: '18:30' } },
      { text: 'MSRH ER and Inpatient MSK (incl. angio studies)', time: 'until 6:30 PM', window: { start: '07:00', end: '18:30' } },
      { text: 'MSRH ER and Inpatient Radiographs', time: 'until 6:30 PM', window: { start: '07:00', end: '18:30' } },
      { text: 'MSRH ER and Inpatient Ultrasound', time: 'until 6:30 PM', window: { start: '07:00', end: '18:30' } },
      { text: 'MSRH MRI for cord compression', time: 'until 6:30 PM', window: { start: '07:00', end: '18:30' } },
      { text: 'Assist UH Neuro weekend residents', time: '7:00 AM – 11:30 AM', note: '(send to Neuro on-call faculty)', window: { start: '07:00', end: '11:30' } },
      { text: 'Assist UH ER 1 and 2 residents', time: '11:30 AM – 6:30 PM', window: { start: '11:30', end: '18:30' } },
    ],
  },
  {
    id: 'nights1',
    eyebrow: 'UH ER Nights · R2',
    title: 'Nights — Resident 1',
    hours: '6:30 PM – 7:00 AM',
    duties: [
      { text: 'ER, Trauma and Inpatient CT Neuro', time: 'until 7:00 AM', note: '(ER Evening Faculty until 9:30, then ER Night Faculty until 4:30 AM)', window: { start: '18:30', end: '07:00' } },
      { text: 'ER and Trauma Radiographs', time: 'until 7:00 AM', note: '(ER Evening Faculty until 9:30, then ER Night Faculty until 4:30 AM)', window: { start: '18:30', end: '07:00' } },
      { text: 'STAT ICU and inpatient Radiographs', time: 'until last modified 4:30 AM', note: '(ER Evening Faculty until 9:30, then ER Night Faculty until 4:30 AM)', window: { start: '18:30', end: '04:30' } },
      { text: 'MRI Cord Compression Protocol', time: 'until 7:00 AM', window: { start: '18:30', end: '07:00' } },
      { text: 'Emergent nuclear medicine studies', note: 'if requested' },
      { text: 'Assist UH ER Nights 2' },
    ],
    footnotes: [
      '*Studies read 4:30 AM – 7:00 AM are assigned to ER Day Faculty.',
      '*Neuro studies read 4:30 AM – 7:00 AM go to designated daytime Neuro Faculty (unless stroke protocol — contact ER Night Faculty for final read).',
    ],
  },
  {
    id: 'nights2',
    eyebrow: 'UH ER Nights · R2',
    title: 'Nights — Resident 2',
    hours: '6:30 PM – 7:00 AM',
    duties: [
      { text: 'ER, Trauma, and Inpatient CT Chest, Abdomen/Pelvis (incl. angio studies)', time: 'until 7:00 AM', note: '(ER Evening Faculty until 9:30, then ER Night Faculty until 4:30 AM)', window: { start: '18:30', end: '07:00' } },
      { text: 'ER, Trauma, and Inpatient CT MSK (incl. angio studies)', time: 'until 7:00 AM', note: '(ER Evening Faculty until 9:30, then ER Night Faculty until 4:30 AM)', window: { start: '18:30', end: '07:00' } },
      { text: 'ER and Trauma Ultrasound', time: 'until 7:00 AM', note: '(ER Evening Faculty until 9:30, then ER Night Faculty until 4:30 AM)', window: { start: '18:30', end: '07:00' } },
      { text: 'Emergent fluoroscopy studies', note: 'if requested' },
      { text: 'Assist UH ER Nights 1' },
    ],
    footnotes: [
      '*Studies read 4:30 AM – 7:00 AM are assigned to ER Day Faculty.',
      '*Neuro studies read 4:30 AM – 7:00 AM go to designated daytime Neuro Faculty (unless stroke protocol — contact ER Night Faculty for final read).',
    ],
  },
  {
    id: 'nights3',
    eyebrow: 'UT ER Nights',
    title: 'Nights — Resident 3',
    hours: '6:30 PM – 7:00 AM',
    duties: [
      { text: 'MSRH ER and Inpatient CT Chest, Abdomen/Pelvis, and Neuro (incl. angio studies)', time: 'until 7:00 AM', window: { start: '18:30', end: '07:00' } },
      { text: 'MSRH ER and Inpatient MSK (incl. angio studies)', time: 'until 7:00 AM', window: { start: '18:30', end: '07:00' } },
      { text: 'MSRH ER and Inpatient Radiographs', time: 'until 7:00 AM', window: { start: '18:30', end: '07:00' } },
      { text: 'MSRH ER and Inpatient Ultrasound', time: 'until 7:00 AM', window: { start: '18:30', end: '07:00' } },
      { text: 'MSRH MRI Cord Compression Protocol', time: 'until 7:00 AM', window: { start: '18:30', end: '07:00' } },
      { text: 'UH STAT Inpatient Radiographs', time: 'until 4:30 AM', note: '(ER Evening Faculty until 9:30, then ER Night Faculty until 4:30 AM)', window: { start: '18:30', end: '04:30' } },
      { text: 'UH Inpatient Ultrasound', time: 'until 4:30 AM', note: '(ER Evening Faculty until 9:30, then ER Night Faculty until 4:30 AM)', window: { start: '18:30', end: '04:30' } },
      { text: 'UH outside interpretation studies (overreads)', time: '6:30 PM – 7:00 AM', note: '(ER Evening Faculty until 9:30, then ER Night Faculty until 4:30 AM)', window: { start: '18:30', end: '07:00' } },
      { text: 'Triage and fully review ALL exams deferred for AM subspecialty read', note: 'for critical or acute findings' },
      { text: 'Manage / decompress the shared list once caught up', note: 'pick up ANY unread CT, Ultrasound, or Radiograph' },
    ],
    footnotes: [
      'Nightly goal: last modified time to resident draft turnaround ≈ 1.5 hours.',
      '*Studies read 4:30 AM – 7:00 AM are assigned to ER Day Faculty.',
      '*Neuro studies read 4:30 AM – 7:00 AM go to designated daytime Neuro Faculty (unless stroke protocol — contact ER Night Faculty for final read).',
    ],
  },
]

const exceptions = [
  'Inpatient CT Chest, Abdomen/Pelvis for <span class="hl hl-metastatic">metastatic workup</span> — must fully <span class="hl hl-metastatic">review for acute findings</span>; dictate if any are found.',
  'ER, Trauma, or Inpatient CT MSK with a <span class="hl hl-fracture">KNOWN fracture</span>.',
  '<span class="hl hl-msk">Inpatient MSK plain films</span> of any priority — no longer within scope of the ER rotation.',
  'STAT <span class="hl hl-critical">Inpatient CT</span> without a <span class="hl hl-critical">critical indication</span>.',
  'Routine studies <span class="hl hl-readifcalled">must be read if called</span> by the clinician.',
  'OR Surgical Radiographs <span class="hl hl-readifcalled">must be read if called</span> — promptly <span class="hl hl-notify">notify ER night faculty</span> for final read.',
]

const overreadPolicy = [
  'Read ALL overread requests from trauma — no exceptions.',
  'If someone other than trauma is requesting an overread, only read the study if: 1) there is an available copy of the outside report, AND 2) there is an appropriate indication for an overread (i.e. the provider thinks the outside report is wrong).',
  'Otherwise, offer to rescan the patient and compare with the outside images, OR defer to your specific faculty on whether they\u2019ll overread it (i.e. give the provider the faculty\u2019s number to contact directly).',
]

const activeRole = computed(() => roles.find(r => r.id === selected.value))

// ---- Time-based dimming ---------------------------------------------------

// Ticks so the UI updates live as the clock crosses a window boundary.
const now = ref(new Date())
let timer = null
onMounted(() => {
  timer = setInterval(() => { now.value = new Date() }, 30000) // refresh every 30s
})
onUnmounted(() => {
  if (timer) clearInterval(timer)
})

function toMinutes(hhmm) {
  const [h, m] = hhmm.split(':').map(Number)
  return h * 60 + m
}

const nowMinutes = computed(() => now.value.getHours() * 60 + now.value.getMinutes())

const nowLabel = computed(() =>
  now.value.toLocaleTimeString([], { hour: 'numeric', minute: '2-digit' })
)

// Returns true if `duty` is currently within its active window.
// Duties with no `window` are always considered active (never dimmed).
function isDutyActive(duty) {
  if (!duty.window) return true
  const start = toMinutes(duty.window.start)
  const end = toMinutes(duty.window.end)
  const n = nowMinutes.value

  if (start === end) return true // degenerate/all-day window, treat as active

  if (start < end) {
    // Normal same-day window, e.g. 07:00 -> 18:30
    return n >= start && n < end
  }
  // Overnight window that wraps past midnight, e.g. 18:30 -> 07:00
  return n >= start || n < end
}
</script>

<style scoped>
.responsibilities-directory {
  padding: 20px;
  font-family: 'Inter', sans-serif;
}

.directory-header {
  display: flex;
  align-items: flex-start;
  justify-content: space-between;
  margin-bottom: 20px;
  gap: 16px;
  flex-wrap: wrap;
}

.header-text {
  display: flex;
  flex-direction: column;
  gap: 4px;
}

.directory-title {
  font-size: 18px;
  font-weight: 600;
  color: var(--text-primary);
  margin: 0;
}

.directory-subtitle {
  font-size: 12px;
  color: var(--text-muted);
  margin: 0;
  max-width: 560px;
  line-height: 1.4;
}

.revised-tag {
  font-size: 11px;
  color: var(--text-dim);
  font-family: 'JetBrains Mono', monospace;
  white-space: nowrap;
}

/* SELECTOR */
.selector-grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 14px;
}

.selector-card {
  background: var(--bg-surface);
  border: 1px solid var(--border);
  border-radius: 14px;
  padding: 16px 18px;
  text-align: left;
  cursor: pointer;
  font-family: 'Inter', sans-serif;
  display: flex;
  flex-direction: column;
  gap: 4px;
  transition: border-color 0.15s, background 0.15s;
}

.selector-card:hover {
  border-color: var(--accent);
  background: var(--bg-raised);
}

.role-hours {
  font-size: 12px;
  font-weight: 500;
  color: var(--accent);
  font-family: 'JetBrains Mono', monospace;
}

.role-hours--detail {
  display: inline-block;
  margin-bottom: 14px;
}

/* DETAIL */
.back-btn {
  background: none;
  border: none;
  color: var(--text-muted);
  font-family: 'Inter', sans-serif;
  font-size: 13px;
  cursor: pointer;
  padding: 0;
  margin-bottom: 14px;
  transition: color 0.15s;
}
.back-btn:hover { color: var(--accent); }

.detail-view {
  display: flex;
  flex-direction: column;
  gap: 14px;
}

.card {
  background: var(--bg-surface);
  border: 1px solid var(--border);
  border-radius: 14px;
  padding: 18px 20px;
}

.section-eyebrow {
  display: block;
  font-size: 10px;
  font-weight: 600;
  letter-spacing: 0.12em;
  text-transform: uppercase;
  color: var(--accent);
  font-family: 'JetBrains Mono', monospace;
  margin-bottom: 3px;
}

.card-title {
  font-size: 16px;
  font-weight: 600;
  color: var(--text-primary);
  margin: 0 0 4px;
}

.now-indicator {
  font-size: 11px;
  color: var(--text-dim);
  font-family: 'JetBrains Mono', monospace;
  margin: 0 0 14px;
}

.now-indicator-hint {
  font-family: 'Inter', sans-serif;
  font-style: italic;
  color: var(--text-dim);
}

.duty-list {
  list-style: none;
  margin: 0;
  padding: 0;
  display: flex;
  flex-direction: column;
  gap: 2px;
}

.duty-row {
  display: flex;
  align-items: flex-start;
  gap: 10px;
  padding: 8px 6px;
  border-radius: 8px;
  transition: background 0.15s, opacity 0.2s;
}

.duty-row:hover {
  background: var(--bg-raised);
}

/* Time-based dimming: task doesn't apply to the current timeslot.
   Kept fully readable (not hidden), just visually de-emphasized. */
.duty-row--dim {
  opacity: 0.4;
}

.duty-row--dim:hover {
  opacity: 0.65;
}

.duty-num {
  flex-shrink: 0;
  width: 18px;
  height: 18px;
  border-radius: 5px;
  background: var(--bg-raised);
  color: var(--text-dim);
  font-size: 10px;
  font-weight: 600;
  font-family: 'JetBrains Mono', monospace;
  display: flex;
  align-items: center;
  justify-content: center;
  margin-top: 1px;
}

.duty-text {
  font-size: 13px;
  color: #b4cff8;
  line-height: 1.5;
}

.duty-time {
  font-weight: 700;
  color: #92dce5;
  font-family: 'JetBrains Mono', monospace;
  font-size: 12px;
}

.duty-note {
  color: #6b7c8d;
  font-size: 12px;
  font-style: italic;
}
.exceptions-card :deep(.hl) {
  padding: 1px 5px;
  border-radius: 4px;
  font-weight: 600;
}

.exceptions-card :deep(.hl-metastatic) {
  background: rgba(250, 204, 21, 0.18);
  color: #facc15;
}

.exceptions-card :deep(.hl-fracture) {
  background: rgba(248, 113, 113, 0.18);
  color: #f87171;
}

.exceptions-card :deep(.hl-msk) {
  background: rgba(45, 212, 191, 0.18);
  color: #2dd4bf;
}

.exceptions-card :deep(.hl-critical) {
  background: rgba(248, 113, 113, 0.18);
  color: #fb7185;
}

.exceptions-card :deep(.hl-readifcalled) {
  background: rgba(74, 222, 128, 0.18);
  color: #4ade80;
}

.exceptions-card :deep(.hl-notify) {
  background: rgba(167, 139, 250, 0.18);
  color: #a78bfa;
}

.footnotes {
  margin-top: 14px;
  padding-top: 12px;
  border-top: 1px solid var(--border);
  display: flex;
  flex-direction: column;
  gap: 6px;
}

.footnotes p {
  font-size: 11.5px;
  color: var(--text-dim);
  margin: 0;
  line-height: 1.5;
}

.exceptions-card .card-title,
.overread-card .card-title {
  font-size: 14px;
}

/* TABLE (ER Weekends: Who Initiates Which Study) */
.table-note {
  font-size: 11px;
  color: var(--text-dim);
  font-style: italic;
  margin: 0 0 12px;
}

.init-table {
  width: 100%;
  border-collapse: collapse;
  font-size: 12.5px;
}

.init-table th {
  text-align: left;
  font-size: 10px;
  font-weight: 600;
  letter-spacing: 0.06em;
  text-transform: uppercase;
  color: var(--text-dim);
  font-family: 'JetBrains Mono', monospace;
  padding: 8px 10px;
  border-bottom: 1px solid var(--border);
}

.init-table td {
  padding: 10px;
  vertical-align: top;
  color: var(--text-muted);
  line-height: 1.4;
  border-bottom: 1px solid var(--border);
}

.init-table tr:last-child td {
  border-bottom: none;
}
</style>
