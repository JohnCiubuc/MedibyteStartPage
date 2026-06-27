<template>
  <section class="card">
  <div class="followup">

    <div class="followup-header">
      <p class="section-eyebrow">REMIND ME</p>
      <p class="followup-sub">Schedule a reminder email on any future date</p>
    </div>

    <div class="followup-body">

      <!-- ── Left: fields ── -->
      <div class="fields">

        <div class="field">
          <label class="lbl" for="sf-study-id">Reminder ID</label>
          <input
            id="sf-study-id"
            v-model="form.studyId"
            class="inp"
            :class="{ error: errors.studyId }"
            placeholder="e.g. UH#... Chick fil A"
            autocomplete="off"
            @input="errors.studyId = ''"
          />
          <span v-if="errors.studyId" class="field-error">{{ errors.studyId }}</span>
        </div>

        <div class="field">
          <label class="lbl" for="sf-email">Notify email (use a personal email for now)</label>
          <input
            id="sf-email"
            v-model="form.email"
            class="inp"
            :class="{ error: errors.email }"
            type="email"
            placeholder="you@gmail.com"
            autocomplete="email"
            @input="errors.email = ''"
          />
          <span v-if="errors.email" class="field-error">{{ errors.email }}</span>
        </div>

        <div class="field">
          <label class="lbl">Send on</label>
          <!-- selected date display — always visible, taps into calendar below on mobile -->
          <div class="date-display" :class="{ set: selectedDate, error: errors.date }">
            <span v-if="selectedDate" class="date-display-value">
              {{ formatDisplay(selectedDate) }}
            </span>
            <span v-else class="date-display-placeholder">Pick a date →</span>
            <span v-if="selectedDate" class="date-clear" @click="clearDate" aria-label="Clear date">×</span>
          </div>
          <span v-if="errors.date" class="field-error">{{ errors.date }}</span>
        </div>

        <!-- Calendar — on desktop lives here in-column; on mobile it moves below via CSS -->
        <div class="cal-slot desktop-cal">
          <CalendarPicker
            :selected="selectedDate"
            @select="onDateSelect"
          />
        </div>

        <div class="field">
          <label class="lbl" for="sf-note">Note <span class="lbl-optional">(optional)</span></label>
          <textarea
            id="sf-note"
            v-model="form.note"
            class="inp inp-textarea"
            placeholder="Check biopsy results from Chick Fil A. "
            rows="3"
          />
        </div>

        <!-- Submit -->
        <button
          class="submit-btn"
          :class="submitState"
          :disabled="submitState === 'loading' || submitState === 'success'"
          @click="submit"
        >
          <span v-if="submitState === 'idle'">→ schedule followup</span>
          <span v-else-if="submitState === 'loading'" class="spinner-wrap">
            <span class="spinner" aria-hidden="true" /> scheduling…
          </span>
          <span v-else-if="submitState === 'success'" class="success-wrap">
            <span class="check" aria-hidden="true">✓</span> scheduled
          </span>
          <span v-else-if="submitState === 'error'">⚠ retry</span>
        </button>

        <!-- Confirmation / error banner -->
        <Transition name="banner-fade">
          <div v-if="submitState === 'success'" class="banner banner-success">
            <span class="banner-icon">✓</span>
            <div>
              <p class="banner-title">Followup scheduled</p>
              <p class="banner-detail">
                Reminder for <strong>{{ confirmedStudyId }}</strong> will be sent to
                <strong>{{ confirmedEmail }}</strong> on <strong>{{ confirmedDate }}</strong>
              </p>
            </div>
            <button class="banner-reset" @click="reset">+ new</button>
          </div>
          <div v-else-if="submitState === 'error'" class="banner banner-error">
            <span class="banner-icon">⚠</span>
            <div>
              <p class="banner-title">Failed to schedule</p>
              <p class="banner-detail">{{ apiError }}</p>
            </div>
          </div>
        </Transition>

      </div>

      <!-- ── Right: calendar (desktop column) ── -->
      <div class="cal-slot mobile-cal">
        <CalendarPicker
          :selected="selectedDate"
          @select="onDateSelect"
        />
      </div>

    </div>
  </div>
  </section>
</template>

<script setup>
import { ref, reactive } from 'vue'
import CalendarPicker from './CalendarPicker.vue'

const API_BASE = "https://medibyte.app/api";

const form = reactive({
  studyId: '',
  email: '',
  note: '',
})
const selectedDate   = ref(null)   // Date object
const submitState    = ref('idle') // 'idle' | 'loading' | 'success' | 'error'
const apiError       = ref('')
const errors         = reactive({ studyId: '', email: '', date: '' })

// Confirmed values stored at submit time for the success banner
const confirmedStudyId = ref('')
const confirmedEmail   = ref('')
const confirmedDate    = ref('')

function formatDisplay(date) {
  return date.toLocaleDateString('en-US', {
    weekday: 'short', month: 'short', day: 'numeric', year: 'numeric'
  })
}

function formatISO(date) {
  // YYYY-MM-DD in local time
  const y = date.getFullYear()
  const m = String(date.getMonth() + 1).padStart(2, '0')
  const d = String(date.getDate()).padStart(2, '0')
  return `${y}-${m}-${d}`
}

function onDateSelect(date) {
  selectedDate.value = date
  errors.date = ''
}

function clearDate() {
  selectedDate.value = null
}

function validate() {
  let ok = true
  if (!form.studyId.trim()) {
    errors.studyId = 'Study ID is required'
    ok = false
  }
  if (!form.email.trim()) {
    errors.email = 'Email is required'
    ok = false
  } else if (!/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(form.email)) {
    errors.email = 'Enter a valid email'
    ok = false
  }
  if (!selectedDate.value) {
    errors.date = 'Pick a date'
    ok = false
  } else {
    const today = new Date(); today.setHours(0,0,0,0)
    if (selectedDate.value <= today) {
      errors.date = 'Date must be in the future'
      ok = false
    }
  }
  return ok
}

async function submit() {
  if (!validate()) return

  submitState.value = 'loading'
  apiError.value = ''

  const payload = {
    study_id: form.studyId.trim(),
    email:    form.email.trim(),
    send_on:  formatISO(selectedDate.value),
    note:     form.note.trim() || null,
  }

  try {
    const res = await fetch(`${API_BASE}/wet-read/followup`, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(payload),
    })

    if (!res.ok) {
      const body = await res.json().catch(() => ({}))
      throw new Error(body.detail ?? `Server error ${res.status}`)
    }

    // Store for banner before clearing
    confirmedStudyId.value = payload.study_id
    confirmedEmail.value   = payload.email
    confirmedDate.value    = formatDisplay(selectedDate.value)

    submitState.value = 'success'

  } catch (err) {
    apiError.value    = err.message ?? 'Unknown error — check your connection'
    submitState.value = 'error'
  }
}

function reset() {
  form.studyId = ''
  form.note    = ''
  selectedDate.value = null
  submitState.value  = 'idle'
  apiError.value     = ''
  Object.assign(errors, { studyId: '', email: '', date: '' })
}
</script>

<style scoped>
/* ── Variables (mirror your app palette) ── */
.followup {
  --accent:        #4a9ea6;
  --accent-bright: #7ec4cb;
  --accent-glow:   rgba(74, 158, 166, 0.15);
  --bg-surface:    var(--bg-surface, #13151a);
  --bg-field:      rgba(0, 0, 0, 0.25);
  --border:        rgba(255, 255, 255, 0.08);
  --border-accent: rgba(74, 158, 166, 0.4);
  --error-color:   #e05a5a;
  --success-color: #4aab82;

  width: 100%;
  display: flex;
  flex-direction: column;
  gap: 20px;
}

.card {
  width: 100%;
  background: var(--bg-surface);
  border: 1px solid var(--border);
  border-radius: 14px;
  padding: var(--card-padding);
}

.section-eyebrow {
  display: block;
  font-size: 10px;
  font-weight: 600;
  letter-spacing: 0.12em;
  text-transform: uppercase;
  color: var(--accent);
  font-family: 'JetBrains Mono', monospace;
  margin-bottom: 4px;
}

/* ── Header ── */
.followup-eyebrow {
  font-family: 'JetBrains Mono', monospace;
  font-size: 9px;
  letter-spacing: 0.2em;
  color: var(--text-dim);
  margin-bottom: 4px;
}
.followup-sub {
  font-family: 'JetBrains Mono', monospace;
  font-size: 11px;
  color: var(--text-primary);
}

/* ── Body: two-column on desktop ── */
.followup-body {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 24px;
  align-items: start;
}

/* ── Fields column ── */
.fields {
  display: flex;
  flex-direction: column;
  gap: 14px;
}

.field {
  display: flex;
  flex-direction: column;
  gap: 5px;
}

.lbl {
  font-family: 'JetBrains Mono', monospace;
  font-size: 9px;
  letter-spacing: 0.14em;
  text-transform: uppercase;
  color: var(--text-primary);
}
.lbl-optional {
  text-transform: none;
  letter-spacing: 0;
  font-size: 9px;
  color: var(--text-primary);
  opacity: 0.5;
}

.inp {
  background: var(--bg-field);
  border: 1px solid var(--border);
  border-radius: 5px;
  padding: 9px 12px;
  font-family: 'JetBrains Mono', monospace;
  font-size: 12px;
  color: var(--text-primary);
  outline: none;
  transition: border-color 0.15s;
  width: 100%;
}
.inp::placeholder { color: var(--text-dim); }
.inp:focus { border-color: var(--accent); }
.inp.error { border-color: var(--error-color); }
.inp-textarea { resize: vertical; min-height: 72px; line-height: 1.5; }

.field-error {
  font-family: 'JetBrains Mono', monospace;
  font-size: 10px;
  color: var(--error-color);
}

/* ── Date display row ── */
.date-display {
  background: var(--bg-field);
  border: 1px solid var(--border);
  border-radius: 5px;
  padding: 9px 12px;
  font-family: 'JetBrains Mono', monospace;
  font-size: 12px;
  display: flex;
  align-items: center;
  justify-content: space-between;
  min-height: 38px;
}
.date-display.set   { border-color: var(--border-accent); }
.date-display.error { border-color: var(--error-color); }
.date-display-value    { color: var(--accent-bright); }
.date-display-placeholder { color: var(--text-dim); }
.date-clear {
  color: var(--text-dim);
  cursor: pointer;
  font-size: 16px;
  line-height: 1;
  padding-left: 8px;
  transition: color 0.15s;
}
.date-clear:hover { color: var(--text-primary); }

/* ── Calendar slot visibility ── */
/* Desktop: calendar lives in right column (.mobile-cal), hide .desktop-cal */
.desktop-cal { display: none; }
.mobile-cal  { display: block; }

/* ── Submit button ── */
.submit-btn {
  background: var(--accent-glow);
  border: 1px solid var(--border-accent);
  border-radius: 5px;
  padding: 11px 16px;
  font-family: 'JetBrains Mono', monospace;
  font-size: 11px;
  letter-spacing: 0.14em;
  color: var(--accent-bright);
  cursor: pointer;
  width: 100%;
  text-align: center;
  transition: background 0.15s, opacity 0.15s;
  margin-top: 4px;
}
.submit-btn:hover:not(:disabled) { background: rgba(74, 158, 166, 0.22); }
.submit-btn:disabled { opacity: 0.6; cursor: default; }
.submit-btn.success {
  background: rgba(74, 171, 130, 0.12);
  border-color: rgba(74, 171, 130, 0.4);
  color: var(--success-color);
}
.submit-btn.error {
  background: rgba(224, 90, 90, 0.1);
  border-color: rgba(224, 90, 90, 0.4);
  color: var(--error-color);
}

.spinner-wrap, .success-wrap {
  display: flex; align-items: center; justify-content: center; gap: 8px;
}

.spinner {
  display: inline-block;
  width: 11px; height: 11px;
  border: 1.5px solid var(--border-accent);
  border-top-color: var(--accent-bright);
  border-radius: 50%;
  animation: spin 0.7s linear infinite;
}
@keyframes spin { to { transform: rotate(360deg); } }

.check { font-size: 13px; }

/* ── Confirmation / error banner ── */
.banner {
  border-radius: 6px;
  padding: 12px 14px;
  display: flex;
  align-items: flex-start;
  gap: 10px;
  font-family: 'JetBrains Mono', monospace;
  font-size: 11px;
}
.banner-success {
  background: rgba(74, 171, 130, 0.08);
  border: 1px solid rgba(74, 171, 130, 0.3);
}
.banner-error {
  background: rgba(224, 90, 90, 0.08);
  border: 1px solid rgba(224, 90, 90, 0.3);
}
.banner-icon {
  font-size: 14px;
  flex-shrink: 0;
  margin-top: 1px;
}
.banner-success .banner-icon { color: var(--success-color); }
.banner-error   .banner-icon { color: var(--error-color); }
.banner-title {
  font-size: 11px;
  color: var(--text-primary);
  margin-bottom: 3px;
}
.banner-detail {
  font-size: 10px;
  color: var(--text-muted);
  line-height: 1.5;
}
.banner-detail strong { color: var(--accent-bright); font-weight: 400; }
.banner-reset {
  margin-left: auto;
  background: none;
  border: 1px solid var(--border);
  border-radius: 4px;
  padding: 4px 10px;
  font-family: 'JetBrains Mono', monospace;
  font-size: 10px;
  color: var(--text-muted);
  cursor: pointer;
  white-space: nowrap;
  flex-shrink: 0;
}
.banner-reset:hover { border-color: var(--accent); color: var(--accent-bright); }

/* ── Transition ── */
.banner-fade-enter-active, .banner-fade-leave-active {
  transition: opacity 0.3s ease, transform 0.3s ease;
}
.banner-fade-enter-from, .banner-fade-leave-to {
  opacity: 0; transform: translateY(-6px);
}

/* ── Mobile: single column ── */
@media (max-width: 640px) {
  .followup-body {
    grid-template-columns: 1fr;
  }
  /* On mobile, hide the right-column cal slot, show the in-field one */
  .desktop-cal { display: block; }
  .mobile-cal  { display: none; }
}
</style>
