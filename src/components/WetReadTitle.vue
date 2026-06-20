
<template>
  <div class="title-band" :class="{ lit: isLit }">
    <div class="content">
      <!-- Logo: aperture / lightbox dial that doubles as a W -->
      <div class="mark-wrap">
        <svg
          class="mark"
          viewBox="0 0 120 120"
          xmlns="http://www.w3.org/2000/svg"
          aria-hidden="true"
        >
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
 
          <!-- glow bloom -->
          <circle cx="60" cy="60" r="56" fill="url(#wr-glow)" />
 
          <!-- outer dial ring with tick marks, like a viewbox brightness dial -->
          <circle
            cx="60" cy="60" r="46"
            fill="none"
            stroke="var(--ring-faint)"
            stroke-width="1"
          />
          <g class="ticks" stroke="var(--ring-faint)" stroke-width="1.5">
            <line v-for="n in 24" :key="n"
              :x1="60 + 46 * Math.cos((n * 15) * Math.PI / 180)"
              :y1="60 + 46 * Math.sin((n * 15) * Math.PI / 180)"
              :x2="60 + 41 * Math.cos((n * 15) * Math.PI / 180)"
              :y2="60 + 41 * Math.sin((n * 15) * Math.PI / 180)"
            />
          </g>
 
          <!-- iris arcs forming the aperture -->
          <g class="iris">
            <path d="M60 18 A42 42 0 0 1 96.4 76.5 L67 60 Z" fill="var(--iris-blade)" />
            <path d="M96.4 76.5 A42 42 0 0 1 23.6 76.5 L60 60 Z" fill="var(--iris-blade-2)" />
            <path d="M23.6 76.5 A42 42 0 0 1 60 18 L53 60 Z" fill="var(--iris-blade)" />
          </g>
 
          <!-- negative-space W cut from the center, lit like a screen -->
          <circle cx="60" cy="60" r="26" fill="var(--well)" />
          <!--
            W centered exactly on cx=60. Outer feet at 60±18 (42,78), outer peaks
            at 60±18 height-matched, inner valley meets at 60. Symmetric about x=60.
          -->
          <path
            class="w-mark"
            d="M42 49 L51 71 L60 53 L69 71 L78 49"
            fill="none"
            stroke="url(#wr-ring-grad)"
            stroke-width="4.25"
            stroke-linecap="round"
            stroke-linejoin="round"
          />
 
          <!-- scan sweep line -->
          <line class="sweep" x1="14" y1="60" x2="106" y2="60" stroke="var(--accent)" stroke-width="0.75" opacity="0.5" />
        </svg>
      </div>
 
      <div class="text-block">
        <p class="eyebrow">
          <span class="dot" /> ON CALL &middot; AFTER HOURS
        </p>
 
        <h1 class="wordmark">
          <span class="the">The</span>
          <span class="wet">Wet</span>
          <span class="read">Read</span>
        </h1>
      </div>
 
      <div class="hairline" />
 
      <p class="timestamp">{{ displayTime }}</p>
    </div>
  </div>
</template>
 
<script setup>
import { ref, onMounted, onUnmounted } from 'vue'
 
const isLit = ref(false)
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
    setTimeout(() => { isLit.value = true }, 60)
  })
})
 
onUnmounted(() => {
  if (clockId) clearInterval(clockId)
})
</script>
 
<style scoped>
.title-band {
  /* derived from host palette: --bg-base, --bg-surface, --accent, --text-* */
  --accent-bright: #7ec4cb;     /* lightened --accent for highlights */
  --well: #0b0e15;              /* slightly deeper than --bg-base, for the lit center */
  --ring-faint: rgba(74, 158, 166, 0.28);
  --iris-blade: rgba(74, 158, 166, 0.12);
  --iris-blade-2: rgba(74, 158, 166, 0.2);
 
  position: relative;
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
  gap: 1.1rem;
  padding: 0.5rem 1rem;
  flex-wrap: wrap;
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
 
.iris {
  transform-origin: 60px 60px;
  transition: opacity 0.8s ease;
}
 
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
}
 
.eyebrow {
  font-family: 'JetBrains Mono', 'SF Mono', Menlo, monospace;
  font-size: 0.62rem;
  letter-spacing: 0.24em;
  color: var(--text-muted);
  margin: 0 0 0.2rem;
  display: flex;
  align-items: center;
  gap: 0.5em;
  opacity: 0;
  transform: translateY(4px);
  transition: opacity 0.6s ease 0.3s, transform 0.6s ease 0.3s;
}
.lit .eyebrow { opacity: 1; transform: translateY(0); }
 
.dot {
  width: 5px;
  height: 5px;
  border-radius: 50%;
  background: var(--accent);
  box-shadow: 0 0 6px 1px var(--accent-glow);
  animation: pulse 2.4s ease-in-out infinite;
}
@keyframes pulse {
  0%, 100% { opacity: 1; }
  50% { opacity: 0.35; }
}
 
/* ---------- Wordmark ---------- */
.wordmark {
  margin: 0;
  font-weight: 400;
  line-height: 0.95;
  display: flex;
  align-items: baseline;
  flex-wrap: wrap;
  gap: 0 0.38em;
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
  font-size: clamp(1.7rem, 3.4vw, 2.3rem);
  font-style: italic;
  color: var(--accent-bright);
  text-shadow: 0 0 18px var(--accent-glow);
  letter-spacing: -0.01em;
}
 
.read {
  font-size: clamp(1.7rem, 3.4vw, 2.3rem);
  color: var(--text-primary);
  letter-spacing: -0.01em;
}
 
/* ---------- Hairline + timestamp ---------- */
.hairline {
  width: 1px;
  height: 30px;
  background: linear-gradient(to bottom, transparent, var(--border-light), transparent);
  margin: 0 0.4rem;
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
  .mark-wrap, .wordmark span, .eyebrow, .hairline, .timestamp {
    transition: opacity 0.3s ease !important;
    transform: none !important;
    filter: none !important;
  }
  .sweep { animation: none; opacity: 0.4; }
  .dot { animation: none; }
}
 
/* ---------- Mobile: stack and tighten further ---------- */
@media (max-width: 480px) {
  .content {
    gap: 0.6rem;
    padding: 0.35rem 0.5rem;
  }
  .mark-wrap { width: 44px; height: 44px; }
  .wet, .read { font-size: 1.5rem; }
  .the { font-size: 0.7rem; margin-bottom: 0.32rem; }
  .hairline { display: none; }
  .timestamp { display: none; }
}
</style>
