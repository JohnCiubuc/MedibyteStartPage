<template>
  <section class="card">
    <span class="section-eyebrow">{{ eyebrow }}</span>
    <div class="card-header">
      <h2 class="card-title">{{ title }}</h2>
      <input v-model="filter" class="group-filter" type="text" placeholder="Filter…" />
    </div>
    <div v-if="filteredTiles.length" class="tiles-grid">
      <a v-for="tile in filteredTiles" :key="tile.label" :href="tile.url" class="tile">
        <div class="tile-icon">
          <img :src="tile.icon" :alt="tile.label" class="tile-img" />
        </div>
        <span class="tile-label">{{ tile.label }}</span>
      </a>
    </div>
    <p v-else class="no-results">No tiles match "{{ filter }}"</p>
  </section>
</template>

<script setup>
import { ref, computed } from 'vue'

const props = defineProps({
  eyebrow: { type: String, default: '' },
  title: { type: String, required: true },
  tiles: { type: Array, required: true },
})

const filter = ref('')
const filteredTiles = computed(() => {
  const q = filter.value.toLowerCase().trim()
  return q ? props.tiles.filter(t => t.label.toLowerCase().includes(q)) : props.tiles
})
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
  margin-bottom: 4px;
}

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

@media (max-height: 800px) {
  .card-header { margin-bottom: 10px; }
  .section-eyebrow { margin-bottom: 2px; }
}

@media (max-height: 620px) {
  .card-header { margin-bottom: 6px; }
  .group-filter { padding: 5px 10px; font-size: 11px; }
}

.tile {
  display: flex;
  flex-direction: column;
  align-items: center;
  text-decoration: none;
  gap: 8px;
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

.tile-label {
  font-size: 10.5px;
  font-weight: 400;
  color: var(--text-muted);
  text-align: center;
  line-height: 1.3;
  width: var(--tile-width)*2;
  white-space: normal;
  overflow: visible;
  text-overflow: unset;
  display: -webkit-box;
  -webkit-line-clamp: 3;
  -webkit-box-orient: vertical;
  overflow: hidden;
  transition: color 0.18s;
}

.tile:hover .tile-label { color: var(--text-primary); }

.no-results {
  font-size: 12px;
  color: var(--text-dim);
  font-style: italic;
  padding: 8px 0;
}
.tile-img {
  width: 90%;
  height: 90%;
  object-fit: contain;
  border-radius: 4px;
}

@media (max-height: 800px) {
  .tile-img { width: 90%; height: 90%; }
}
</style>
