<template>
  <section class="quick-access">
    <span class="section-eyebrow">Quick Access</span>
    <div class="tiles-row">
      <a
        v-for="tile in tiles"
        :key="tile.label"
        :href="tile.url"
        class="tile"
      >
<div class="tile-icon">
  <img :src="tile.icon" :alt="tile.label" class="tile-img" />
</div>
        <span class="tile-label">{{ tile.label }}</span>
      </a>
    </div>
  </section>
</template>

<script setup>
defineProps({
  tiles: { type: Array, required: true },
})
</script>

<style scoped>
.quick-access {
  width: 100%;
  margin-top: 4px;
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

.tiles-row {
  display: flex;
  flex-direction: row;
  flex-wrap: wrap;

  /* gap: var(--tile-gap); */
  gap: 32px;
}
/* Tighten filter input label on short screens */
@media (max-height: 800px) {
  .card-header { margin-bottom: 10px; }
  .section-eyebrow { margin-bottom: 2px; }
}

@media (max-height: 620px) {
  .card-header { margin-bottom: 6px; }
  .group-filter { padding: 5px 10px; font-size: 11px; }
}

.tile {
  flex: 0 0 auto;
  width: var(--tile-width);
  gap: var(--tile-gap);
  display: flex;
  flex-direction: column;
  align-items: center;
  text-decoration: none;
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
  max-width: 80px;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
  transition: color 0.18s;
}

.tile:hover .tile-label { color: var(--text-primary); }
.tile-img {
  width: 80%;
  height: 80%;
  object-fit: contain;
  border-radius: 4px;
}

</style>
