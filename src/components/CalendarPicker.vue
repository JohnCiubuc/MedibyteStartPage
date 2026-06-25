<template>
  <div class="cal">
    <!-- Month navigation header -->
    <div class="cal-header">
      <button class="cal-nav" @click="prevMonth" aria-label="Previous month">‹</button>
      <span class="cal-month-label">{{ monthLabel }}</span>
      <button class="cal-nav" @click="nextMonth" aria-label="Next month">›</button>
    </div>

    <!-- Day-of-week headers -->
    <div class="cal-grid">
      <div v-for="h in DOW_HEADERS" :key="h" class="cal-dow">{{ h }}</div>

      <!-- Empty cells before month starts -->
      <div v-for="n in startOffset" :key="'e' + n" class="cal-cell empty" />

      <!-- Day cells -->
      <button
        v-for="day in daysInMonth"
        :key="day"
        class="cal-cell"
        :class="{
          selected: isSelected(day),
          today:    isToday(day),
          past:     isPast(day),
        }"
        :disabled="isPast(day)"
        :aria-label="ariaLabel(day)"
        :aria-pressed="isSelected(day)"
        @click="select(day)"
      >{{ day }}</button>
    </div>

    <!-- Quick-jump shortcuts -->
    <div class="cal-shortcuts">
      <button
        v-for="s in shortcuts"
        :key="s.label"
        class="shortcut"
        :class="{ active: isShortcutActive(s) }"
        @click="applyShortcut(s)"
      >{{ s.label }}</button>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'

const props = defineProps({
  selected: { type: Date, default: null },
})
const emit = defineEmits(['select'])

const DOW_HEADERS = ['S','M','T','W','T','F','S']

// Viewport month — starts at current month
const viewYear  = ref(new Date().getFullYear())
const viewMonth = ref(new Date().getMonth()) // 0-indexed

const monthLabel = computed(() => {
  return new Date(viewYear.value, viewMonth.value, 1)
    .toLocaleDateString('en-US', { month: 'long', year: 'numeric' })
    .toUpperCase()
})

const daysInMonth = computed(() =>
  new Date(viewYear.value, viewMonth.value + 1, 0).getDate()
)

const startOffset = computed(() =>
  new Date(viewYear.value, viewMonth.value, 1).getDay()
)

function prevMonth() {
  if (viewMonth.value === 0) { viewMonth.value = 11; viewYear.value-- }
  else viewMonth.value--
}
function nextMonth() {
  if (viewMonth.value === 11) { viewMonth.value = 0; viewYear.value++ }
  else viewMonth.value++
}

// ── Date helpers ──
function cellDate(day) {
  return new Date(viewYear.value, viewMonth.value, day)
}
function todayMidnight() {
  const t = new Date(); t.setHours(0,0,0,0); return t
}
function isSelected(day) {
  if (!props.selected) return false
  const d = cellDate(day)
  return d.toDateString() === props.selected.toDateString()
}
function isToday(day) {
  return cellDate(day).toDateString() === new Date().toDateString()
}
function isPast(day) {
  return cellDate(day) <= todayMidnight()
}
function ariaLabel(day) {
  return cellDate(day).toLocaleDateString('en-US', { month: 'long', day: 'numeric', year: 'numeric' })
}
function select(day) {
  if (isPast(day)) return
  emit('select', cellDate(day))
}

// ── Quick shortcuts ──
const shortcuts = [
  { label: '+2 dy',  days: 2   },
  { label: '+3 dy',  days: 3   },
  { label: '+1 wk',  days: 7   },
  { label: '+2 wk',  days: 14  },
  { label: '+1 mo',  months: 1 },
  { label: '+3 mo',  months: 3 },
  { label: '+6 mo',  months: 6 },
  { label: '+1 yr',  months: 12 },
]
function shortcutDate(s) {
  const d = new Date()
  if (s.days)   d.setDate(d.getDate() + s.days)
  if (s.months) d.setMonth(d.getMonth() + s.months)
  return d
}
function applyShortcut(s) {
  const d = shortcutDate(s)
  // Jump calendar view to that month
  viewYear.value  = d.getFullYear()
  viewMonth.value = d.getMonth()
  emit('select', d)
}
function isShortcutActive(s) {
  if (!props.selected) return false
  return shortcutDate(s).toDateString() === props.selected.toDateString()
}
</script>

<style scoped>
.cal {
  background: var(--bg-surface, #13151a);
  border: 1px solid rgba(255,255,255,0.08);
  border-radius: 8px;
  padding: 14px;
  user-select: none;
}

/* ── Header ── */
.cal-header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  margin-bottom: 12px;
}
.cal-month-label {
  font-family: 'JetBrains Mono', monospace;
  font-size: 10px;
  letter-spacing: 0.14em;
  color: var(--text-muted, #7a8a8b);
}
.cal-nav {
  background: none;
  border: none;
  color: var(--text-dim, #3d4f50);
  font-size: 16px;
  cursor: pointer;
  padding: 2px 6px;
  border-radius: 4px;
  line-height: 1;
  transition: color 0.15s, background 0.15s;
}
.cal-nav:hover {
  color: var(--accent-bright, #7ec4cb);
  background: rgba(74,158,166,0.1);
}

/* ── Grid ── */
.cal-grid {
  display: grid;
  grid-template-columns: repeat(7, 1fr);
  gap: 2px;
}
.cal-dow {
  font-family: 'JetBrains Mono', monospace;
  font-size: 8px;
  letter-spacing: 0.08em;
  color: var(--text-dim, #3d4f50);
  text-align: center;
  padding: 3px 0 6px;
}
.cal-cell {
  font-family: 'JetBrains Mono', monospace;
  font-size: 11px;
  color: var(--text-muted, #7a8a8b);
  text-align: center;
  aspect-ratio: 1;
  display: flex;
  align-items: center;
  justify-content: center;
  border-radius: 4px;
  border: none;
  background: none;
  cursor: pointer;
  transition: background 0.1s, color 0.1s;
}
.cal-cell.empty { cursor: default; pointer-events: none; }
.cal-cell:hover:not(.past):not(.empty) {
  background: rgba(74,158,166,0.12);
  color: var(--accent-bright, #7ec4cb);
}
.cal-cell.today {
  color: var(--accent-bright, #7ec4cb);
  font-weight: 600;
}
.cal-cell.past {
  color: var(--text-dim, #3d4f50);
  cursor: default;
  opacity: 0.4;
}
.cal-cell.selected {
  background: var(--accent, #4a9ea6);
  color: #fff;
  font-weight: 600;
}
.cal-cell.selected:hover { background: var(--accent-bright, #7ec4cb); }

/* ── Shortcuts ── */
.cal-shortcuts {
  display: flex;
  flex-wrap: wrap;
  gap: 5px;
  margin-top: 12px;
  padding-top: 10px;
  border-top: 1px solid rgba(255,255,255,0.06);
}
.shortcut {
  background: none;
  border: 1px solid rgba(255,255,255,0.07);
  border-radius: 4px;
  padding: 4px 9px;
  font-family: 'JetBrains Mono', monospace;
  font-size: 9px;
  letter-spacing: 0.1em;
  color: var(--text-dim, #3d4f50);
  cursor: pointer;
  transition: border-color 0.15s, color 0.15s, background 0.15s;
}
.shortcut:hover {
  border-color: rgba(74,158,166,0.35);
  color: var(--accent-bright, #7ec4cb);
  background: rgba(74,158,166,0.07);
}
.shortcut.active {
  border-color: var(--accent, #4a9ea6);
  color: var(--accent-bright, #7ec4cb);
  background: rgba(74,158,166,0.12);
}
</style>
