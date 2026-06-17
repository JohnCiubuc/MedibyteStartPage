<template>
<div class="select-none font-mono">
    <!-- Header -->
    <div class="mb-6 text-center">
      <h3 class="text-2xl font-bold text-white tracking-tight mb-4">MRI {{ bodyPart }} Anatomy</h3>
      <p class="text-gray-500 text-xs mt-4">Scroll or drag the image to navigate slices</p>
    </div>


    <!-- Image viewer -->
    <div
      ref="viewerRef"
class="relative w-[200px] bg-black rounded-lg overflow-hidden border border-gray-800 cursor-ns-resize shadow-2xl shadow-black" 
      @wheel.prevent="onWheel"
      @mousedown="onMouseDown"
      @touchstart.prevent="onTouchStart"
      @touchmove.prevent="onTouchMove"
      @touchend="onTouchEnd"
    >
      <!-- Images -->
      <img
        v-for="i in imageIndices"
        :key="i"
        :src="imageUrl(i)"
        :alt="`${bodyPart} slice ${i}`"
class="block w-full h-auto object-contain" style="width: 700px;"
        v-show="i === currentIndex"
        draggable="false"
      />

      <!-- Drag indicator overlay (shows while dragging) -->
      <Transition name="fade">
        <div
          v-if="isDragging"
          class="absolute inset-0 border-2 border-cyan-500/40 rounded-lg pointer-events-none"
        />
      </Transition>

      <!-- Slice label -->
      <!-- <div class="absolute bottom-3 right-3 bg-black/70 text-cyan-400 text-xs px-2 py-1 rounded font-mono"> -->
      <!--   Slice {{ currentIndex }} / {{ maxNumber }} -->
      <!-- </div> -->
    </div>

    <!-- Sensitivity hint (dev-facing) -->
    <!-- <p class="text-gray-700 text-xs mt-4"> -->
    <!--   Drag sensitivity: <code class="text-gray-500">{{ dragSensitivity }}px / slice</code> -->
    <!-- </p> -->
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'

// ─── Props ───────────────────────────────────────────────────────────────────
const props = defineProps({
  bodyPart: {
    type: String,
    default: 'Knee',
  },
  maxNumber: {
    type: Number,
    default: 9,
  },
  /**
   * Pixels of drag distance required to advance one slice.
   * Tune this manually — lower = more sensitive, higher = less sensitive.
   */
  dragSensitivity: {
    type: Number,
    default: 30,
  },
})

// ─── State ───────────────────────────────────────────────────────────────────
const currentIndex = ref(0)
const viewerRef = ref(null)

// Indices array for v-for rendering
const imageIndices = computed(() =>
  Array.from({ length: props.maxNumber + 1 }, (_, i) => i)
)

// Progress bar width %
const progressPercent = computed(() =>
  props.maxNumber === 0 ? 0 : (currentIndex.value / props.maxNumber) * 100
)

// Image URL builder — mirrors the original pattern
function imageUrl(index) {
  return `https://freitasrad.net/pages/atlas/${props.bodyPart}/${index}.jpg`
}

// ─── Navigation ──────────────────────────────────────────────────────────────
function next() {
  currentIndex.value = currentIndex.value >= props.maxNumber ? 0 : currentIndex.value + 1
}

function prev() {
  currentIndex.value = currentIndex.value <= 0 ? props.maxNumber : currentIndex.value - 1
}

// ─── Mouse wheel ─────────────────────────────────────────────────────────────
function onWheel(e) {
  if (e.deltaY > 0) next()
  else prev()
}

// ─── Click-and-drag ──────────────────────────────────────────────────────────
const isDragging = ref(false)
let dragStartY = 0
let dragAccumulator = 0

function onMouseDown(e) {
  isDragging.value = true
  dragStartY = e.clientY
  dragAccumulator = 0
  document.body.style.userSelect = 'none'

  const onMove = (ev) => {
    const delta = ev.clientY - dragStartY
    dragAccumulator += delta
    dragStartY = ev.clientY

    // Advance slices based on accumulated drag distance
    while (dragAccumulator <= -props.dragSensitivity) {
      prev()
      dragAccumulator += props.dragSensitivity
    }
    while (dragAccumulator >= props.dragSensitivity) {
      next()
      dragAccumulator -= props.dragSensitivity
    }
  }

  const onUp = () => {
    isDragging.value = false
    document.body.style.userSelect = ''
    window.removeEventListener('mousemove', onMove)
    window.removeEventListener('mouseup', onUp)
  }

  window.addEventListener('mousemove', onMove)
  window.addEventListener('mouseup', onUp)
}

// ─── Touch drag ──────────────────────────────────────────────────────────────
let touchStartY = 0
let touchAccumulator = 0

function onTouchStart(e) {
  touchStartY = e.touches[0].clientY
  touchAccumulator = 0
}

function onTouchMove(e) {
  const delta = e.touches[0].clientY - touchStartY
  touchAccumulator += delta
  touchStartY = e.touches[0].clientY

  while (touchAccumulator <= -props.dragSensitivity) {
    prev()
    touchAccumulator += props.dragSensitivity
  }
  while (touchAccumulator >= props.dragSensitivity) {
    next()
    touchAccumulator -= props.dragSensitivity
  }
}

function onTouchEnd() {
  touchAccumulator = 0
}
</script>

<style scoped>
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.1s;
}
.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}
</style>
