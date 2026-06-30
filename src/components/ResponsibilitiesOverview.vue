<template>
  <div class="responsibilities-directory">
    <div class="directory-header">
      <h1 class="directory-title">ER Resident Responsibilities</h1>
      <span class="revised-tag">Last Revised April 2025</span>
    </div>

    <!-- SELECTOR -->
    <div v-if="!selected" class="selector-grid">
      <button
        v-for="role in roles"
        :key="role.id"
        class="selector-card"
        @click="selected = role.id"
      >
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

        <ol class="duty-list">
          <li v-for="(d, i) in activeRole.duties" :key="i" class="duty-row">
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

      <section class="card exceptions-card">
        <span class="section-eyebrow">All Shifts</span>
        <h2 class="card-title">Exceptions — Daytime Assignment OK If</h2>
        <ol class="duty-list">
          <li v-for="(e, i) in exceptions" :key="i" class="duty-row">
            <span class="duty-num">{{ i + 1 }}</span>
            <div class="duty-text">{{ e }}</div>
          </li>
        </ol>
        <div class="footnotes">
          <p>When leaving a study for AM subspecialty interpretation you <strong>MUST</strong> leave a study comment and assign the study to the correct subspecialty.</p>
        </div>
      </section>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'

const selected = ref(null)

const roles = [
  {
    id: 'trieve',
    eyebrow: 'Weekday Evening',
    title: 'TRIEVE',
    hours: '4:30 PM – 9:30 PM',
    duties: [
      { text: 'STAT MSRH studies', time: 'until 6:30 PM' },
      { text: 'Inpatient CT Chest, Abdomen/Pelvis (incl. angio studies)', time: 'until 6:30 PM' },
      { text: 'ER, Trauma, and Inpatient CT Neuro', time: 'until 6:30 PM' },
      { text: 'MRI Cord Compression Protocol', time: 'until 6:30 PM' },
      { text: 'ICU plain films', time: 'until 9:30 PM' },
      { text: 'Inpatient and Inpatient Pedi plain films', time: 'until 9:30 PM' },
      { text: 'Outside interpretation studies (overreads)', time: 'until 6:30 PM' },
    ],
  },
  {
    id: 'triage',
    eyebrow: 'Weekday Evening',
    title: 'TRIAGE',
    hours: '4:30 PM – 6:30 PM',
    duties: [
      { text: 'ER and Trauma CT Chest, Abdomen/Pelvis (incl. angio studies)', time: 'until 6:30 PM' },
      { text: 'ER, Trauma, and Inpatient CT MSK (incl. angio studies)', time: 'until 6:30 PM' },
      { text: 'ER and Trauma Ultrasound', time: 'until 6:30 PM' },
      { text: 'ER and Trauma Radiographs', time: 'until 6:30 PM' },
      { text: 'Emergent Fluoroscopy and Nuclear Medicine Studies', time: 'until 6:30 PM' },
    ],
  },
  {
    id: 'weekend1',
    eyebrow: 'UH ER Weekend',
    title: 'Weekend 1',
    hours: '7:00 AM – 6:30 PM',
    duties: [
      { text: 'ALL Inpatient Pediatric Radiographs', time: 'until last modified 8:00 AM', note: '(AM2 Faculty)' },
      { text: 'ER and Trauma CT Chest, Abdomen/Pelvis (incl. angio studies)', time: 'until 6:30 PM', note: '(AM1 Faculty until 2:30 PM, then ER Evening Faculty)' },
      { text: 'ER, Trauma, and Inpatient MSK (incl. angio studies)', time: 'until 6:30 PM', note: '(AM1 until 2:30 PM, then ER Evening)' },
      { text: 'ER, Trauma, and Inpatient Ultrasound', time: 'until 6:30 PM', note: '(AM1 until 2:30 PM, then ER Evening)' },
      { text: 'Emergent Fluoro and Nuclear Medicine Studies', time: 'until 6:30 PM', note: '(AM1 until 2:30 PM, then ER Evening)' },
      { text: 'Outside Read Interpretations', time: 'until 6:30 PM', note: '(AM1 until 2:30 PM, then ER Evening)' },
      { text: 'Inpatient CT Chest, Abdomen/Pelvis', time: '2:30 PM – 6:30 PM', note: '(ER Evening Faculty)' },
    ],
  },
  {
    id: 'weekend2',
    eyebrow: 'UH ER Weekend',
    title: 'Weekend 2',
    hours: '7:00 AM – 6:30 PM',
    duties: [
      { text: 'STAT Inpatient Pediatric Radiographs', time: 'starts 8:00 AM (last modified) until 6:30 PM', note: '(AM2 until 2:30 PM, then ER Evening)' },
      { text: 'STAT Inpatient Radiographs', time: 'until last modified 2:30 PM', note: '(AM2 Faculty)' },
      { text: 'ALL ICU Radiographs', time: 'until last modified 2:30 PM', note: '(AM2 Faculty)' },
      { text: 'ER and Trauma Radiographs', time: 'starts 12:00 PM until 6:30 PM', note: '(AM2 until 2:30 PM, then ER Evening)' },
      { text: 'Inpatient CT Chest, Abdomen/Pelvis', time: 'until 2:30 PM', note: '(AM1 Faculty)' },
      { text: 'ER, Trauma, and Inpatient CT Neuro', time: 'starts 2:30 PM until 6:30 PM', note: '(ER Evening Faculty)' },
      { text: 'MRI Cord Compression Protocol', time: 'starts 2:30 PM until 6:30 PM', note: '(ER Evening Faculty)' },
    ],
  },
  {
    id: 'weekend3',
    eyebrow: 'UT ER Weekend',
    title: 'Weekend 3',
    hours: '7:00 AM – 6:30 PM',
    duties: [
      { text: 'MSRH ER and Inpatient CT Chest, Abdomen/Pelvis, and Neuro (incl. angio studies)', time: 'until 6:30 PM' },
      { text: 'MSRH ER and Inpatient MSK (incl. angio studies)', time: 'until 6:30 PM' },
      { text: 'MSRH ER and Inpatient Radiographs', time: 'until 6:30 PM' },
      { text: 'MSRH ER and Inpatient Ultrasound', time: 'until 6:30 PM' },
      { text: 'MSRH MRI for cord compression', time: 'until 6:30 PM' },
      { text: 'Assist UH Neuro weekend residents', time: '7:00 AM – 11:30 AM' },
      { text: 'Assist UH ER 1 and 2 residents', time: '11:30 AM – 6:30 PM' },
    ],
  },
  {
    id: 'nights1',
    eyebrow: 'UH ER Nights · R2',
    title: 'Nights — Resident 1',
    hours: '6:30 PM – 7:00 AM',
    duties: [
      { text: 'ER, Trauma and Inpatient CT Neuro', time: 'until 7:00 AM', note: '(ER Evening until 9:30, then ER Night until 4:30 AM)' },
      { text: 'ER and Trauma Radiographs', time: 'until 7:00 AM', note: '(ER Evening until 9:30, then ER Night until 4:30 AM)' },
      { text: 'STAT ICU Radiographs', time: 'until last modified 4:30 AM', note: '(ER Evening until 9:30, then ER Night until 4:30 AM)' },
      { text: 'Emergent nuclear medicine studies', note: 'if requested' },
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
      { text: 'ER, Trauma, and Inpatient CT Chest, Abdomen/Pelvis (incl. angio studies)', time: 'until 7:00 AM', note: '(ER Evening until 9:30, then ER Night until 4:30 AM)' },
      { text: 'ER, Trauma, and Inpatient CT MSK (incl. angio studies)', time: 'until 7:00 AM', note: '(ER Evening until 9:30, then ER Night until 4:30 AM)' },
      { text: 'ER and Trauma Ultrasound', time: 'until 7:00 AM', note: '(ER Evening until 9:30, then ER Night until 4:30 AM)' },
      { text: 'Emergent fluoroscopy studies', note: 'if requested' },
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
      { text: 'MSRH ER and Inpatient CT Chest, Abdomen/Pelvis, and Neuro (incl. angio studies)', time: 'until 7:00 AM' },
      { text: 'MSRH ER and Inpatient MSK (incl. angio studies)', time: 'until 7:00 AM' },
      { text: 'MSRH ER and Inpatient Radiographs', time: 'until 7:00 AM' },
      { text: 'MSRH ER and Inpatient Ultrasound', time: 'until 7:00 AM' },
      { text: 'MSRH MRI Cord Compression Protocol', time: 'until 7:00 AM' },
      { text: 'UH STAT Inpatient Radiographs', time: 'until 4:30 AM', note: '(ER Evening until 9:30, then ER Night until 4:30 AM)' },
      { text: 'UH Inpatient Ultrasound', time: 'until 4:30 AM', note: '(ER Evening until 9:30, then ER Night until 4:30 AM)' },
      { text: 'UH outside interpretation studies (overreads)', time: '6:30 PM – 7:00 AM', note: '(ER Evening until 9:30, then ER Night until 4:30 AM)' },
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
  'Inpatient CT Chest, Abdomen/Pelvis for metastatic workup — must fully review for acute findings; dictate if any are found.',
  'ER, Trauma, or Inpatient CT MSK with a KNOWN fracture.',
  'Inpatient MSK plain films of any priority — no longer within scope of the ER rotation.',
  'STAT Inpatient CT without a critical indication.',
  'Routine studies must be read if called by the clinician.',
  'OR Surgical Radiographs must be read if called — promptly notify ER night faculty for final read.',
]

const activeRole = computed(() => roles.find(r => r.id === selected.value))
</script>

<style scoped>
.responsibilities-directory {
  padding: 20px;
  font-family: 'Inter', sans-serif;
}

.directory-header {
  display: flex;
  align-items: baseline;
  justify-content: space-between;
  margin-bottom: 20px;
  gap: 16px;
  flex-wrap: wrap;
}

.directory-title {
  font-size: 18px;
  font-weight: 600;
  color: var(--text-primary);
  margin: 0;
}

.revised-tag {
  font-size: 11px;
  color: var(--text-dim);
  font-family: 'JetBrains Mono', monospace;
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
  transition: background 0.15s;
}

.duty-row:hover {
  background: var(--bg-raised);
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
  color: var(--text-muted);
  line-height: 1.5;
}

.duty-time {
  font-weight: 700;
  color: var(--text-primary);
  font-family: 'JetBrains Mono', monospace;
  font-size: 12px;
}

.duty-note {
  color: var(--text-dim);
  font-size: 12px;
  font-style: italic;
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

.exceptions-card .card-title {
  font-size: 14px;
}
</style>
