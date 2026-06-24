<template>
  <div class="visit-counter">
    <span class="visit-label">visitors</span>
    <span class="visit-divider">·</span>
    <span class="visit-count">{{ displayCount }}</span>
  </div>
</template>

<script>
export default {
  name: 'VisitCounter',
  data() {
    return {
      visitCount: null,
      error: false,
    }
  },
  computed: {
    displayCount() {
      if (this.error) return '—'
      if (this.visitCount === null) return '···'
      return Number(this.visitCount).toLocaleString()
    }
  },
  async mounted() {
    try {
      const res = await fetch('https://medibyte.app/api/wet-read/visit')
      if (!res.ok) throw new Error()
      const data = await res.json()
      this.visitCount = data.data.up_count
    } catch {
      this.error = true
    }
  }
}
</script>

<style scoped>
.visit-counter {
  display: flex;
  align-items: center;
  gap: 6px;
  font-family: 'JetBrains Mono', monospace;
  font-size: 11px;
  color: var(--text-dim);
  opacity: 0.7;
}

.visit-label {
  text-transform: uppercase;
  letter-spacing: 0.12em;
  font-size: 9px;
  opacity: 0.6;
}

.visit-divider {
  opacity: 0.3;
}

.visit-count {
  color: var(--accent);
  opacity: 0.8;
  letter-spacing: 0.05em;
  font-variant-numeric: tabular-nums;
}
</style>
