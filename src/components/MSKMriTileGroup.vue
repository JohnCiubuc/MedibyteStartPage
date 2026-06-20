<template>
  <section class="card">
    <span class="section-eyebrow">{{ eyebrow }}</span>

    <!-- ── Tile grid view ── -->
    <template v-if="!activePart">
      <div class="card-header">
        <h2 class="card-title">{{ title }}</h2>
        <!-- <input v-model="filter" class="group-filter" type="text" placeholder="Filter…" /> -->
      </div>

      <div v-if="filteredTiles.length" class="tiles-grid">
        <button
          v-for="tile in filteredTiles"
          :key="tile.label"
          class="tile"
          @click="openViewer(tile)"
        >
          <div class="tile-icon">
            <img :src="tile.icon" :alt="tile.label" class="tile-img" />
          </div>
          <span class="tile-label">{{ tile.label }}</span>
        </button>
      </div>
      <p v-else class="no-results">No tiles match "{{ filter }}"</p>
    </template>

    <!-- ── Viewer view ── -->
    <template v-else>
      <div class="viewer-header mt-4">
        <button class="back-btn mt-4" @click="closeViewer">
          <svg width="12" height="12" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round">
            <path d="M19 12H5M12 5l-7 7 7 7"/>
          </svg>
          Go back
        </button>
        <h2 class="card-title">MRI {{ activePart.label }}</h2>
        <a href="https://freitasrad.net/" class="learn-more">Go to FreitasRad.net</a>

      </div>

      <MriAtlasViewer
        :body-part="activePart.label"
        :max-number="activePart.maxNumber"
        :drag-sensitivity="30"
      />
    </template>
  </section>
</template>

<script setup>
import { ref, computed } from 'vue'
import MriAtlasViewer from '@/components/MriAtlasViewer.vue'
const props = defineProps({
  eyebrow: { type: String, default: '' },
  title:   { type: String, default: 'MSK MRI Atlas' },
  tiles: { type: Array, required: true },
  filter: { type: String, default: '' },
})

// ─── MSK body parts ──────────────────────────────────────────────────────────
// Adjust maxNumber per part to match the actual slice counts on the server.

// ─── Filter ──────────────────────────────────────────────────────────────────
// 
const filteredTiles = computed(() => {
  const q = props.filter.toLowerCase().trim()
  return q
    ? props.tiles.filter(t =>
        t.label.toLowerCase().includes(q) ||
        (t.keywords && t.keywords.toLowerCase().includes(q))
      )
    : props.tiles
})

// ─── Viewer state ────────────────────────────────────────────────────────────
const activePart = ref(null)

function openViewer(tile) {
  activePart.value = tile
}

function closeViewer() {
  activePart.value = null
}
</script>

<style scoped>
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
  margin-bottom: 12px;
}

/* ── Tile grid header ── */
.card-header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 10px;
  margin-bottom: 14px;
}

.card-title {
  font-size: 15px;
  font-weight: 500;
  color: var(--text-primary);
  margin: 0;
}

.group-filter {
  background: var(--bg-raised);
  border: 1px solid var(--border);
  color: var(--text-primary);
  font-family: 'Inter', sans-serif;
  font-size: 12px;
  padding: 7px 12px;
  border-radius: 7px;
  outline: none;
  max-width: 180px;
  transition: border-color 0.2s;
}
.group-filter::placeholder { color: var(--text-dim); }
.group-filter:focus { border-color: var(--accent); }

/* ── Tiles ── */
.tiles-grid {
  display: grid;
  grid-template-columns: repeat(6, 1fr);
  gap: var(--tile-gap, 12px);
  padding: 4px;
}

.tile {
  display: flex;
  flex-direction: column;
  align-items: center;
  background: none;
  border: none;
  cursor: pointer;
  gap: 8px;
  padding: 4px;
}
.tile-icon {
  width: var(--tile-icon-size);
  height: var(--tile-icon-size);
  font-size: var(--tile-icon-font);
  background: var(--bg-raised);
  border: 1px solid var(--border);
  border-radius: 14px;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: background 0.18s, border-color 0.18s, transform 0.15s, box-shadow 0.18s;
  position: relative;
  overflow: hidden;
}

.tile-icon::after {
  content: '';
  position: absolute;
  inset: 0;
  background: linear-gradient(135deg, rgba(255,255,255,0.04) 0%, transparent 60%);
  border-radius: 14px;
}

.tile:hover .tile-icon {
  background: var(--bg-hover);
  border-color: var(--border-light);
  transform: translateY(-2px);
  box-shadow: 0 6px 20px rgba(0, 0, 0, 0.35);
}

.tile-img {
  width: 90%;
  height: 90%;
  object-fit: contain;
  border-radius: 4px;
}

.tile-label {
  font-size: 10.5px;
  font-weight: 400;
  color: var(--text-muted);
  text-align: center;
  line-height: 1.3;
  transition: color 0.18s;
  margin-bottom: 10px;
}
.tile:hover .tile-label { color: var(--text-primary); }

.no-results {
  font-size: 12px;
  color: var(--text-dim);
  font-style: italic;
  padding: 8px 0;
}

/* ── Viewer header ── */
.viewer-header {
  display: flex;
  align-items: center;
  gap: 12px;
  margin-bottom: 14px;
}

.back-btn {
  display: inline-flex;
  align-items: center;
  gap: 5px;
  padding: 5px 10px;
  font-size: 11px;
  font-weight: 500;
  font-family: 'Inter', sans-serif;
  color: var(--text-muted);
  background: var(--bg-raised);
  border: 1px solid var(--border);
  border-radius: 7px;
  cursor: pointer;
  transition: color 0.18s, border-color 0.18s, background 0.18s;
  white-space: nowrap;
}

.back-btn:hover {
  color: var(--text-primary);
  border-color: var(--accent);
  background: var(--bg-hover);
}
.learn-more {
  background: none;
  border: none;
  color: var(--accent);
  font-family: 'JetBrains Mono', monospace;
  font-size: 11px;
  cursor: pointer;
  padding: 0;
  margin-left: 4px;
  text-decoration: underline;
  text-underline-offset: 2px;
}

.learn-more:hover { opacity: 0.75; }
.tiles-grid {
  display: grid;
  grid-template-columns: repeat(8, 1fr);
  gap: var(--tile-gap);
  max-height: calc(var(--tile-icon-size) * 3.8);
  overflow-y: auto;
  padding: 4px;
}

.tiles-grid::-webkit-scrollbar { width: 4px; }
.tiles-grid::-webkit-scrollbar-track { background: transparent; }
.tiles-grid::-webkit-scrollbar-thumb { background: var(--border); border-radius: 2px; }
</style>
