<template>
  <form class="search-wrapper" @submit.prevent="onSubmit">
    <input
      v-model="query"
      class="search-input"
      type="text"
      placeholder="Search the web…"
      autofocus
    />
    <button class="search-btn" type="submit" aria-label="Search">
      <svg width="18" height="18" viewBox="0 0 24 24" fill="none"
           stroke="currentColor" stroke-width="2"
           stroke-linecap="round" stroke-linejoin="round">
        <circle cx="11" cy="11" r="8" />
        <line x1="21" y1="21" x2="16.65" y2="16.65" />
      </svg>
    </button>
  </form>
<p class="hint">
  Powered by Loose Bangs!
  <button class="learn-more" @click="showHelp = !showHelp">Learn more</button>
</p>
<div v-if="showHelp" class="help-panel">
  <p class="help-title">Loose Bangs — quick prefixes</p>
  <table class="help-table">
    <tbody>
      <tr v-for="bang in bangs" :key="bang.prefix">
        <td class="help-prefix">{{ bang.prefix }} …</td>
        <td class="help-desc">{{ bang.label }}</td>
        <td class="help-example">e.g. "{{ bang.example }}"</td>
      </tr>
    </tbody>
  </table>
  <p class="help-footer">No prefix? Falls back to Google.</p>
</div>
</template>

<script setup>
import { ref } from 'vue'

const query    = ref('')
const showHelp = ref(false)

const bangs = [
  { prefix: 'r',  label: 'Radiopaedia',         example: 'r emphysema',         url: q => `https://radiopaedia.org/search?q=${q}` },
  { prefix: 'o',  label: 'OpenEvidence',    example: 'o hot quadrate sign pathophys',          url: q => `https://www.openevidence.com/ask?query=${q}` },
  { prefix: 'g',  label: 'Google Scholar',               example: 'g HCC treatment 2026',       url: q => 'https://scholar.google.com/scholar?q=${q}' },
  // { prefix: 'g',  label: 'Google',               example: 'g pneumothorax',      url: q => `https://www.google.com/search?q=${q}` },
  // { prefix: 'yt', label: 'YouTube',              example: 'yt radiology review', url: q => `https://www.youtube.com/results?search_query=${q}` },
  // { prefix: 'w',  label: 'Wikipedia',            example: 'w aortic stenosis',   url: q => `https://en.wikipedia.org/wiki/Special:Search?search=${q}` },
  // 
  { prefix: 'p',  label: 'PubMed',               example: 'p uroepithelial carcinoma',    url: q => `https://pubmed.ncbi.nlm.nih.gov/?term=${q}` },
]

function resolve(raw) {
  const trimmed = raw.trim()
  // match a prefix word followed by a space and at least one char
  const match = trimmed.match(/^([a-z]+)\s+(.+)$/i)
  if (match) {
    const prefix = match[1].toLowerCase()
    const rest   = encodeURIComponent(match[2])
    const bang   = bangs.find(b => b.prefix === prefix)
    if (bang) return bang.url(rest)
  }
  // fallback → Google
  return `https://www.google.com/search?q=${encodeURIComponent(trimmed)}`
}

function onSubmit() {
  if (query.value.trim()) {
    window.location = resolve(query.value)
    query.value = ''
  }
}
</script>

<style scoped>
.search-wrapper {
  position: relative;
  width: 100%;
  max-width: 580px;
  margin-bottom: 4px;

  margin-left: auto;
  margin-right: auto;
}

.search-input {
  width: 100%;
  background: var(--bg-surface);
  border: 1px solid var(--border);
  color: var(--text-primary);
  font-family: 'Inter', sans-serif;
  font-size: 15px;
  padding: 13px 52px 13px 20px;
  border-radius: 10px;
  outline: none;
  transition: border-color 0.2s, box-shadow 0.2s;
}

.search-input::placeholder { color: var(--text-dim); }

.search-input:focus {
  border-color: var(--accent);
  box-shadow: 0 0 0 3px var(--accent-glow);
}

.search-btn {
  position: absolute;
  right: 10px;
  top: 50%;
  transform: translateY(-50%);
  background: none;
  border: none;
  cursor: pointer;
  color: var(--text-muted);
  display: flex;
  align-items: center;
  padding: 4px;
  transition: color 0.2s;
}

.search-btn:hover { color: var(--accent); }
@media (max-height: 800px) {
  .search-input { padding: 10px 46px 10px 16px; font-size: 14px; }
  .search-wrapper { margin-bottom: 0; }
}

@media (max-height: 620px) {
  .search-input { padding: 8px 40px 8px 14px; font-size: 13px; }
}

.hint {
  font-size: 11px;
  color: var(--text-dim);
  font-family: 'JetBrains Mono', monospace;
  text-align: center;
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

.help-panel {
  margin-top: 12px;
  width: 100%;
  max-width: 580px;
  background: var(--bg-surface);
  border: 1px solid var(--border);
  border-radius: 10px;
  padding: 14px 18px;
}

.help-title {
  font-size: 11px;
  font-weight: 600;
  color: var(--text-muted);
  font-family: 'JetBrains Mono', monospace;
  text-transform: uppercase;
  letter-spacing: 0.08em;
  margin-bottom: 10px;
}

.help-table {
  width: 100%;
  border-collapse: collapse;
  font-size: 12px;
}

.help-table tr + tr td { padding-top: 6px; }

.help-prefix {
  font-family: 'JetBrains Mono', monospace;
  color: var(--accent);
  width: 48px;
  vertical-align: top;
}

.help-desc {
  color: var(--text-primary);
  padding: 0 12px;
  vertical-align: top;
}

.help-example {
  color: var(--text-dim);
  font-family: 'JetBrains Mono', monospace;
  font-size: 11px;
  vertical-align: top;
}

.help-footer {
  margin-top: 10px;
  font-size: 11px;
  color: var(--text-dim);
  font-family: 'JetBrains Mono', monospace;
}
</style>
