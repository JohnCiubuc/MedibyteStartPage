<template>
  <div class="title-band" :class="{ lit: isLit }">
    <div class="content">
      <div class="mark-wrap" ref="markRef">
        <svg class="mark" viewBox="0 0 120 120" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
          <defs>
            <radialGradient id="wr-glow" cx="50%" cy="50%" r="50%">
              <stop offset="0%" stop-color="var(--accent)" stop-opacity="0.5" />
              <stop offset="60%" stop-color="var(--accent)" stop-opacity="0.1" />
              <stop offset="100%" stop-color="var(--accent)" stop-opacity="0" />
            </radialGradient>
            <linearGradient id="wr-ring-grad" x1="0%" y1="0%" x2="100%" y2="100%">
              <stop offset="0%" stop-color="var(--accent-bright)" />
              <stop offset="100%" stop-color="var(--accent)" />
            </linearGradient>
          </defs>
          <circle cx="60" cy="60" r="56" fill="url(#wr-glow)" />
          <circle cx="60" cy="60" r="46" fill="none" stroke="var(--ring-faint)" stroke-width="1" />
          <g class="ticks" stroke="var(--ring-faint)" stroke-width="1.5">
            <line v-for="n in 24" :key="n"
              :x1="60 + 46 * Math.cos((n * 15) * Math.PI / 180)"
              :y1="60 + 46 * Math.sin((n * 15) * Math.PI / 180)"
              :x2="60 + 41 * Math.cos((n * 15) * Math.PI / 180)"
              :y2="60 + 41 * Math.sin((n * 15) * Math.PI / 180)"
            />
          </g>
          <g class="iris">
            <path d="M60 18 A42 42 0 0 1 96.4 76.5 L67 60 Z" fill="var(--iris-blade)" />
            <path d="M96.4 76.5 A42 42 0 0 1 23.6 76.5 L60 60 Z" fill="var(--iris-blade-2)" />
            <path d="M23.6 76.5 A42 42 0 0 1 60 18 L53 60 Z" fill="var(--iris-blade)" />
          </g>
          <circle cx="60" cy="60" r="26" fill="var(--well)" />
          <path class="w-mark" d="M42 49 L51 71 L60 53 L69 71 L78 49" fill="none" stroke="url(#wr-ring-grad)" stroke-width="4.25" stroke-linecap="round" stroke-linejoin="round" />
          <line class="sweep" x1="14" y1="60" x2="106" y2="60" stroke="var(--accent)" stroke-width="0.75" opacity="0.5" />
        </svg>
      </div>

      <div class="main-content">
        <nav class="mode-tabs" aria-label="Site mode">
          <button class="mode-tab" :class="{ active: currentMode === 'quickaccess' }" @click="$emit('modeChange', 'quickaccess')" aria-current="currentMode === 'quickaccess' ? 'page' : undefined">
            <span class="tab-dot" aria-hidden="true" />
            QUICK ACCESS
          </button>
          <span class="tab-sep" aria-hidden="true">·</span>
          <button class="mode-tab" :class="{ active: currentMode === 'followup' }" @click="$emit('modeChange', 'followup')" aria-current="currentMode === 'followup' ? 'page' : undefined">
            <span class="tab-dot" aria-hidden="true" />
            STUDY FOLLOWUP
          </button>
          <span class="tab-sep" aria-hidden="true">·</span>
          <button class="mode-tab" :class="{ active: currentMode === 'caselog' }" @click="$emit('modeChange', 'caselog')" aria-current="currentMode === 'caselog' ? 'page' : undefined">
            <span class="tab-dot" aria-hidden="true" />
            COMMUNITY CASELOG
          </button>
        </nav>

        <h1 class="wordmark">
          <span class="the">The</span>
          <span class="wet">Wet</span>
          <span class="read">Read</span>
        </h1>
      </div>

      <div class="time-section">
        <div class="hairline" />
        <p class="timestamp">{{ displayTime }}</p>
      </div>
    </div>
  </div>

  <Teleport to="body">
    <div v-if="hasPinged" class="wr-ping-portal" aria-hidden="true" :style="{ '--ping-x': pingX + 'px', '--ping-y': pingY + 'px' }">
      <div class="wr-ping-ring wr-ping-ring-1" />
      <div class="wr-ping-ring wr-ping-ring-2" />
    </div>
  </Teleport>
</template>

<script setup>
import { ref, onMounted, onUnmounted } from 'vue'

const props = defineProps({
  currentMode: {
    type: String,
    default: 'quickaccess'
  }
})

defineEmits(['modeChange'])

const isLit    = ref(false)
const hasPinged = ref(false)
const pingX    = ref(0)
const pingY    = ref(0)
const markRef  = ref(null)
const displayTime = ref('')
let clockId = null

function formatTime() {
  const now = new Date()
  const time = now.toLocaleTimeString('en-US', {
    hour: '2-digit',
    minute: '2-digit',
    hour12: false,
  })
  const date = now.toLocaleDateString('en-US', {
    weekday: 'short',
    month: 'short',
    day: 'numeric',
  })
  displayTime.value = `${date} — ${time}`
}

onMounted(() => {
  formatTime()
  clockId = setInterval(formatTime, 1000 * 30)
  requestAnimationFrame(() => {
    setTimeout(() => {
      isLit.value = true
      setTimeout(() => {
        // Measure the logo's center in viewport coordinates at fire time
        if (markRef.value) {
          const rect = markRef.value.getBoundingClientRect()
          pingX.value = Math.round(rect.left + rect.width / 2)
          pingY.value = Math.round(rect.top  + rect.height / 2)
        }
        hasPinged.value = true
      }, 1150)
    }, 60)
  })
})

onUnmounted(() => {
  if (clockId) clearInterval(clockId)
})
</script>

<style scoped>
.title-band {
  --accent-bright: #7ec4cb;
  --well: #0b0e15;
  --ring-faint: rgba(74, 158, 166, 0.28);
  --iris-blade: rgba(74, 158, 166, 0.12);
  --iris-blade-2: rgba(74, 158, 166, 0.2);

  position: relative;
  overflow: hidden;
  display: flex;
  align-items: center;
  justify-content: center;
  width: 100%;
  background: transparent;
  font-family: 'Iowan Old Style', 'Georgia', serif;
}

.content {
  position: relative;
  z-index: 1;
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 0.5rem;
  padding: 0.5rem 1rem;
  width: 100%;
  max-width: 1400px;
  margin: 0 auto;
}
.main-content {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 0.75rem;
  flex: 1;
  min-width: 0;
}

/* ---------- Logo ---------- */
.mark-wrap {
  width: 56px;
  height: 56px;
  flex: none;
  opacity: 0;
  transform: scale(0.85);
  filter: brightness(0.5);
  transition: opacity 0.8s ease, transform 0.8s cubic-bezier(.2,.8,.2,1), filter 1s ease;
}
.lit .mark-wrap {
  opacity: 1;
  transform: scale(1);
  filter: brightness(1);
}

.mark { width: 100%; height: 100%; display: block; }
.iris { transform-origin: 60px 60px; transition: opacity 0.8s ease; }
.ticks { opacity: 0.6; }

.w-mark {
  stroke-dasharray: 90;
  stroke-dashoffset: 90;
  transition: stroke-dashoffset 0.9s ease 0.35s;
}
.lit .w-mark { stroke-dashoffset: 0; }

.sweep {
  transform-origin: 60px 60px;
  animation: sweep-rotate 8s linear infinite;
  opacity: 0;
}
.lit .sweep {
  opacity: 0.5;
  animation: sweep-rotate 8s linear infinite, sweep-in 0.8s ease 0.45s;
}

@keyframes sweep-rotate {
  from { transform: rotate(0deg); }
  to { transform: rotate(360deg); }
}
@keyframes sweep-in {
  from { opacity: 0; }
  to { opacity: 0.5; }
}

/* ---------- Text block ---------- */
.text-block {
  display: flex;
  flex-direction: column;
  align-items: flex-start;
  gap: 0.75rem;
}

/* ---------- Mode tabs ---------- */

.mode-tabs {
  display: flex;
  align-items: center;
  gap: 0.4em;
  opacity: 0;
  transform: translateY(4px);
  transition: opacity 0.6s ease 0.3s, transform 0.6s ease 0.3s;
  justify-content: center;
  flex-wrap: nowrap;
}
.lit .mode-tabs { opacity: 1; transform: translateY(0); }

.tab-sep {
  font-family: 'JetBrains Mono', 'SF Mono', Menlo, monospace;
  font-size: 0.7rem;
  color: var(--text-dim);
  user-select: none;
  opacity: 0.4;
}

.mode-tab {
  background: rgba(255, 255, 255, 0.03);
  border: 1px solid rgba(255, 255, 255, 0.08);
  border-radius: 4px;
  padding: 0.3em 0.75em 0.3em 0.6em;
  cursor: pointer;

  font-family: 'JetBrains Mono', 'SF Mono', Menlo, monospace;
  font-size: 0.78rem;
  letter-spacing: 0.18em;
  color: var(--text-muted);

  display: flex;
  align-items: center;
  gap: 0.55em;

  transition: color 0.2s ease, border-color 0.2s ease, background 0.2s ease;
}

.mode-tab:hover {
  color: var(--text-primary);
  border-color: rgba(255, 255, 255, 0.18);
  background: rgba(255, 255, 255, 0.06);
}

.mode-tab.active {
  color: var(--accent-bright);
  border-color: var(--accent);
  background: rgba(74, 158, 166, 0.1);
}

/* no underline pseudo-element needed anymore — border carries it */
.mode-tab::after { display: none; }

/* ---------- Dot — loud, rippling ---------- */
.tab-dot {
  position: relative;
  width: 7px;
  height: 7px;
  flex-shrink: 0;
}

/* the dot itself */
.tab-dot::before {
  content: '';
  position: absolute;
  inset: 0;
  border-radius: 50%;
  background: var(--text-dim);
  transition: background 0.2s ease;
}

/* ripple ring */
.tab-dot::after {
  content: '';
  position: absolute;
  inset: -3px;
  border-radius: 50%;
  border: 1.5px solid transparent;
  transition: border-color 0.2s ease;
}

.mode-tab.active .tab-dot::before {
  background: var(--accent-bright);
  box-shadow: 0 0 0 2px rgba(126, 196, 203, 0.3), 0 0 8px 2px rgba(126, 196, 203, 0.4);
  animation: dot-pulse 4s ease-in-out infinite;
}

.mode-tab.active .tab-dot::after {
  border-color: rgba(126, 196, 203, 0.5);
  animation: ring-expand 2s ease-in-out infinite;
}

@keyframes dot-pulse {
  0%, 100% {
    box-shadow: 0 0 0 2px rgba(126, 196, 203, 0.3), 0 0 8px 2px rgba(126, 196, 203, 0.4);
  }
  50% {
    box-shadow: 0 0 0 3px rgba(126, 196, 203, 0.1), 0 0 14px 4px rgba(126, 196, 203, 0.25);
  }
}

@keyframes ring-expand {
  0% {
    transform: scale(1);
    opacity: 0.8;
  }
  60% {
    transform: scale(2.2);
    opacity: 0;
  }
  100% {
    transform: scale(2.2);
    opacity: 0;
  }
}

/* ---------- Sonar ping (portal — teleported to body, position:fixed) ---------- */
/* Scoped styles don't apply to teleported nodes, so these go in a global
   stylesheet OR you add :deep() / remove scoped for this block.
   Easiest: move these rules into your global CSS (e.g. main.css / index.css).
   They're namespaced with wr-ping- so there's no collision risk.            */

@media (prefers-reduced-motion: reduce) {
  .wr-ping-ring { display: none; }
}
.wordmark {
  margin: 0;
  font-weight: 400;
  line-height: 0.95;
  display: flex;
  align-items: baseline;
  gap: 0 0.38em;
  justify-content: center;
  white-space: nowrap;
}
.wordmark span {
  opacity: 0;
  filter: blur(4px);
  transform: translateY(6px);
  transition: opacity 0.6s ease, filter 0.6s ease, transform 0.6s ease;
}
.lit .wordmark span { opacity: 1; filter: blur(0); transform: translateY(0); }
.lit .wordmark .the  { transition-delay: 0.4s; }
.lit .wordmark .wet  { transition-delay: 0.48s; }
.lit .wordmark .read { transition-delay: 0.56s; }

.the {
  font-size: 0.85rem;
  letter-spacing: 0.16em;
  text-transform: uppercase;
  color: var(--text-dim);
  align-self: flex-end;
  margin-bottom: 0.45rem;
  margin-right: 0.1em;
}
.wet {
  font-size: clamp(2.2rem, 4.5vw, 3rem);
  font-style: italic;
  color: var(--accent-bright);
  text-shadow: 0 0 18px var(--accent-glow);
  letter-spacing: -0.01em;
}
.read {
  font-size: clamp(2.2rem, 4.5vw, 3rem);
  color: var(--text-primary);
  letter-spacing: -0.01em;
}

/* ---------- Hairline + timestamp ---------- */
.time-section {
  display: flex;
  align-items: center;
  gap: 0.4rem;
  flex-shrink: 0;
}

.hairline {
  width: 1px;
  height: 30px;
  background: linear-gradient(to bottom, transparent, var(--border-light), transparent);
  transform: scaleY(0);
  transition: transform 0.5s ease 0.6s;
}
.lit .hairline { transform: scaleY(1); }

.timestamp {
  font-family: 'JetBrains Mono', 'SF Mono', Menlo, monospace;
  font-size: 0.65rem;
  letter-spacing: 0.1em;
  color: var(--text-dim);
  margin: 0;
  opacity: 0;
  transition: opacity 0.6s ease 0.7s;
  white-space: nowrap;
}
.lit .timestamp { opacity: 1; }

/* ---------- Reduced motion ---------- */
@media (prefers-reduced-motion: reduce) {
  .mark-wrap, .wordmark span, .mode-tabs, .hairline, .timestamp {
    transition: opacity 0.3s ease !important;
    transform: none !important;
    filter: none !important;
  }
  .sweep { animation: none; opacity: 0.4; }
  .tab-dot { animation: none; }
}

/* ---------- Mobile ---------- */
@media (max-width: 480px) {
  .content { gap: 0.6rem; padding: 0.35rem 0.5rem; }
  .mark-wrap { width: 44px; height: 44px; }
  .wet, .read { font-size: 1.5rem; }
  .the { font-size: 0.7rem; margin-bottom: 0.32rem; }
  .hairline { display: none; }
  .timestamp { display: none; }
  .mode-tab { font-size: 0.68rem; letter-spacing: 0.12em; padding: 0.25em 0.6em 0.25em 0.5em; }
}
</style>

<!-- NOT scoped — these rules target the teleported node that lives on <body> -->
<style>
.wr-ping-portal {
  position: fixed;
  top: var(--ping-y);
  left: var(--ping-x);
  width: 0;
  height: 0;
  pointer-events: none;
  z-index: 9999;
}

.wr-ping-ring {
  position: absolute;
  /* 60vw diameter — expands well past half the page from the logo origin */
  width: 60vw;
  height: 60vw;
  top: calc(-30vw);
  left: calc(-30vw);
  border-radius: 50%;
  border: 1.5px solid transparent;
  animation: wr-sonar-ping 1.8s cubic-bezier(0.15, 0.5, 0.35, 1) forwards;
}

.wr-ping-ring-2 {
  animation-delay: 0.32s;
}

@keyframes wr-sonar-ping {
  0% {
    transform: scale(0);
    opacity: 0;
    border-color: transparent;
  }
  6% {
    opacity: 1;
    border-color: rgba(126, 196, 203, 0.5);
  }
  100% {
    transform: scale(1);
    opacity: 0;
    border-color: rgba(74, 158, 166, 0);
  }
}

@media (prefers-reduced-motion: reduce) {
  .wr-ping-portal { display: none; }
}
</style>
