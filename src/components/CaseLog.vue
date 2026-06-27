<template>
  <div class="module">

    <Toast v-if="toast.message" :message="toast.message" :type="toast.type" @close="toast.message = ''" />
    <!-- Header -->
    <div class="module-header">
      <div>
        <div class="module-label">Community Case Log</div>
        <div class="module-title">Interesting Studies — Share &amp; Browse</div>
      </div>
      <div class="toolbar">
        <div class="search-wrap">
          <i class="ti ti-search" aria-hidden="true"></i>
          <input v-model="searchQuery" class="search-input" type="text" placeholder="search or #tag..." />
        </div>
        <button class="view-btn" :class="{ active: currentView === 'card' }" title="Card view"
          @click="currentView = 'card'">
          <i class="ti ti-layout-grid" aria-hidden="true"></i>
        </button>
        <button class="view-btn" :class="{ active: currentView === 'list' }" title="Dense list"
          @click="currentView = 'list'">
          <i class="ti ti-list" aria-hidden="true"></i>
        </button>
        <!-- <div class="view-toggle"> -->
        <!-- </div> -->
        <button class="add-btn" @click="submitOpen = !submitOpen">
          <i class="ti ti-plus" aria-hidden="true" style="font-size:12px"></i>
          share case
        </button>
      </div>
    </div>

    <!-- Submit panel -->
    <div class="submit-panel" :class="{ open: submitOpen }">
      <div class="submit-grid">
        <div>
          <div class="field-label">Study ID / Accession</div>
          <input v-model="form.id" class="field-input" type="text" placeholder="e.g. UH4849978" />
        </div>
        <div>
          <div class="field-label">Body System</div>
          <select v-model="form.system" class="field-input">
            <option value="">— select —</option>
            <option v-for="s in systemKeys" :key="s" :value="s">{{ s }}</option>
          </select>
        </div>
        <div class="field-full">
          <div class="field-label">Description — use #tags inline</div>
          <input v-model="form.desc" class="field-input" type="text"
            placeholder="e.g. Saddle PE with right heart strain on CTA #boards #emergency" />
          <div class="hint">
            Anything prefixed with # becomes a searchable tag. Modality goes in the description — no separate field
            needed.
          </div>
        </div>
      </div>
      <div class="pending-note">
        <i class="ti ti-info-circle" aria-hidden="true"></i>
        Submissions are held for review before appearing publicly. This keeps the list clean for everyone.
      </div>
      <div class="pending-note">
        <i class="ti ti-info-circle" aria-hidden="true"></i>
        Do not include any patient identifiable information. By submiting an interesting case, you agree that TheWetRead and its creator assumes no liability for any errors, omissions, or outcomes resulting from the use of the site and its submission system. You accept full responsibility for any actions taken based on information provided. Use of this site is entirely at your own risk.
      </div>
      <div class="submit-row">
        <span class="rl-note">
          <span v-if="rlSeconds > 0" class="rl-timer">wait {{ rlSeconds }}s</span>
        </span>
        <div class="submit-actions">
          <button class="cancel-btn" @click="submitOpen = false">cancel</button>
          <button class="submit-go" :disabled="rlSeconds > 0" @click="submitCase">
            → submit
          </button>
        </div>
      </div>
    </div>

    <!-- Tag bar -->
    <div class="tag-bar">
      <!-- <span class="tag-label">top tags</span> -->
      <!-- <span -->
      <!--   v-for="[tag, count] in topTags" -->
      <!--   :key="tag" -->
      <!--   class="tag-pill" -->
      <!--   :class="{ active: activeTag === tag }" -->
      <!--   @click="activeTag = activeTag === tag ? null : tag" -->
      <!-- > -->
      <!--   #{{ tag }} {{ count }} -->
      <!-- </span> -->
      <!-- <span v-if="extraTagCount > 0" class="tag-more"> -->
      <!--   +{{ extraTagCount }} more — search #tag -->
      <!-- </span> -->
      <!-- <span class="divider-v"></span> -->
      <span v-for="s in systemKeys" :key="s" class="sys-pill" :class="{ active: activeSys === s }"
        :style="sysPillStyle(s, activeSys === s)" @click="activeSys = activeSys === s ? null : s">
        {{ s }}
      </span>
    </div>

    <!-- Stats bar -->
    <div class="stats-bar">
      <div class="stat">
        <div class="stat-n">{{ cases.length }}</div>
        <div class="stat-l">total cases</div>
      </div>
      <div class="stat">
        <div class="stat-n">{{ filtered.length }}</div>
        <div class="stat-l">shown</div>
      </div>
    </div>
    <div v-if="loading" class="loading">
      <i class="ti ti-loader rotating" aria-hidden="true"></i>
      Loading cases...
    </div>
    <!-- List view -->
    <div v-else-if="currentView === 'list'">
      <table class="table-view" aria-label="Community case log">
        <thead>
          <tr>
            <th style="width:130px">Accession</th>
            <th>Description</th>
            <th style="width:76px">System</th>
            <th style="width:58px">Added</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="c in filtered" :key="c.id + c.desc" :class="{ 'flash-copy': flashingId === c.id }"
            title="Click anywhere to copy accession number" @click="handleCopy(c.id)">
            <td>
              <div class="acc-cell">
                <i class="ti ti-clipboard copy-icon" :class="{ 'acc-ok': flashingId === c.id }" aria-hidden="true"></i>
                <span class="acc-text" :class="{ 'acc-ok': flashingId === c.id }">
                  {{ c.id }}
                </span>
              </div>
            </td>
            <td class="td-desc" v-html="renderInlineTags(c.desc)"></td>
            <td class="td-system">
              <span class="sys-badge" :style="sysBadgeStyle(c.system)">{{ c.system }}</span>
            </td>
            <td class="td-age">{{ formatAge(c.daysAgo) }}</td>
          </tr>
        </tbody>
      </table>
      <div v-if="filtered.length === 0" class="empty">
        No cases match — try a different search or #tag.
      </div>
    </div>

    <!-- Card view -->
    <div v-else>
      <div class="card-grid">
        <div v-for="c in filtered" :key="c.id + c.desc" class="case-card" :class="{ 'flash-copy': flashingId === c.id }"
          title="Click to copy accession number" @click="handleCopy(c.id)">
          <div class="card-top">
            <div class="card-id-wrap">
              <i class="ti ti-clipboard copy-icon" :class="{ 'acc-ok': flashingId === c.id }" aria-hidden="true"></i>
              <span class="card-id-text" :class="{ 'acc-ok': flashingId === c.id }">
                {{ c.id }}
              </span>
            </div>
            <span class="card-age">{{ formatAge(c.daysAgo) }}</span>
          </div>
          <div class="card-desc" v-html="renderInlineTags(c.desc)"></div>
          <div class="card-footer">
            <span class="sys-badge" :style="sysBadgeStyle(c.system)">{{ c.system }}</span>
          </div>
        </div>
      </div>
      <div v-if="filtered.length === 0" class="empty">
        No cases match — try a different search or #tag.
      </div>
    </div>

  </div>
</template>

<script setup>
import { ref, computed, onUnmounted , onMounted} from 'vue'
import Toast from './Toast.vue'

// ─── System definitions ───────────────────────────────────────────────────────

const SYSTEMS = {
  abd:     { color: '#c8965a', bg: 'rgba(200,140,70,',  border: 'rgba(200,140,70,.22)'  },
  breast:  { color: '#d49ec8', bg: 'rgba(180,100,170,', border: 'rgba(180,100,170,.22)' },
  cardiac: { color: '#e07a7a', bg: 'rgba(210,90,90,',   border: 'rgba(210,90,90,.22)'   },
  chest:   { color: '#4a9ea6', bg: 'rgba(74,158,166,',  border: 'rgba(74,158,166,.22)'  },
  msk:     { color: '#72b87e', bg: 'rgba(90,160,100,',  border: 'rgba(90,160,100,.22)'  },
  neuro:   { color: '#9b8de8', bg: 'rgba(120,100,200,', border: 'rgba(120,100,200,.25)' },
  nucs:    { color: '#a0c878', bg: 'rgba(140,190,90,',  border: 'rgba(140,190,90,.22)'  },
  peds:    { color: '#78b8d4', bg: 'rgba(90,160,200,',  border: 'rgba(90,160,200,.22)'  },
  pelvis:  { color: '#c47aaa', bg: 'rgba(180,90,140,',  border: 'rgba(180,90,140,.22)'  },
  vasc:    { color: '#d47474', bg: 'rgba(200,80,80,',   border: 'rgba(200,80,80,.22)'   },
}


const systemKeys = Object.keys(SYSTEMS)

function sysBadgeStyle(s) {
  const c = SYSTEMS[s] || { color: '#6b7fa3', bg: 'rgba(107,127,163,', border: 'rgba(107,127,163,.22)' }
  return `color:${c.color};background:${c.bg}0.12);border-color:${c.border}`
}

function sysPillStyle(s, active) {
  const c = SYSTEMS[s] || { color: '#6b7fa3', bg: 'rgba(107,127,163,', border: 'rgba(107,127,163,.22)' }
  const opacity = active ? '0.22' : '0.1'
  return `color:${c.color};background:${c.bg}${opacity});border-color:${c.border};${active ? 'filter:brightness(1.4)' : ''}`
}

// ─── Demo data ────────────────────────────────────────────────────────────────
// Replace with API call to GET /api/cases?visible=1

const cases = ref([
  { id: 'UH4849978', system: 'abd',     desc: 'Pancreatic adenocarcinoma with SMV involvement, classic encasement #teaching #hepatopancreatobiliary', daysAgo: 0 },
  { id: 'UH2938471', system: 'pelvis',  desc: 'Retained surgical sponge left pelvis, gossypiboma with surrounding fibrosis #classic #surgical', daysAgo: 0 },
  { id: 'MR9938271', system: 'chest',   desc: 'Saddle pulmonary embolus with right heart strain on CTA-PE #boards #emergency', daysAgo: 1 },
  { id: 'UH1124509', system: 'neuro',   desc: 'GBM with thick irregular ring-enhancing lesion, central necrosis #teaching #neuro-onc', daysAgo: 1 },
  { id: 'CT7732018', system: 'chest',   desc: 'Crazy-paving pattern bilateral lungs, confirmed PAP on BAL #rare #diffuse-lung #teaching', daysAgo: 2 },
  { id: 'MR4401337', system: 'msk',     desc: 'PVNS of the knee with hemosiderin deposition on GRE sequence #rare', daysAgo: 2 },
  { id: 'UH9901234', system: 'abd',     desc: 'Hepatic adenoma with intratumoral hemorrhage, young woman on OCP #hepato #emergency', daysAgo: 3 },
  { id: 'CT8812990', system: 'vasc',    desc: 'Type B aortic dissection extending to bilateral iliac, managed medically #boards', daysAgo: 3 },
  { id: 'UH3302881', system: 'neuro',   desc: 'Cavernous malformation brainstem with popcorn appearance and hemosiderin rim #classic', daysAgo: 4 },
  { id: 'MR2201455', system: 'pelvis',  desc: 'Cervical cancer with parametrial invasion stage IIB on MRI #gyn-onc #staging', daysAgo: 4 },
  { id: 'NM8810023', system: 'nucs',    desc: 'Hot thyroid nodule on Tc-99m, autonomous functioning adenoma, cold on ultrasound #teaching', daysAgo: 5 },
  { id: 'MR5544120', system: 'cardiac', desc: 'Cardiac sarcoidosis with patchy LGE on CMR, non-ischemic distribution #rare #boards', daysAgo: 5 },
  { id: 'MG1199002', system: 'breast',  desc: 'Spiculated mass with architectural distortion, IDC on core biopsy, BI-RADS 5 #teaching', daysAgo: 6 },
  { id: 'CT3387651', system: 'vasc',    desc: 'Median arcuate ligament syndrome with celiac axis compression on expiration #rare', daysAgo: 7 },
  { id: 'UH7712388', system: 'peds',    desc: 'Intussusception ileocolic in 2yo, classic target sign on ultrasound, air enema reduced #classic #peds', daysAgo: 8 },
  { id: 'UH4422019', system: 'abd',     desc: 'Budd-Chiari syndrome with caudate lobe hypertrophy and hepatic vein occlusion #rare #hepato', daysAgo: 9 },
  { id: 'NM9901123', system: 'nucs',    desc: 'FDG-avid mediastinal and hilar nodes, Deauville 4 on interim PET, lymphoma #boards', daysAgo: 10 },
  { id: 'MR7733445', system: 'cardiac', desc: 'ARVD with fatty replacement RV free wall on MRI, epsilon wave on ECG correlation #rare', daysAgo: 12 },
])

// ─── State ────────────────────────────────────────────────────────────────────

const searchQuery = ref('')
const activeTag   = ref(null)
const activeSys   = ref(null)
const currentView = ref('card')
const flashingId  = ref(null)
const submitOpen  = ref(false)
const rlSeconds   = ref(0)
let rlInterval    = null
// const cases = ref([])
const loading = ref(true)

const form = ref({ id: '', system: '', desc: '' })

const toast = ref({ message: '', type: 'info' })

function showToast(message, type = 'info') {
  toast.value = { message, type }
}

// ─── Helpers ──────────────────────────────────────────────────────────────────

function extractTags(desc) {
  return (desc.match(/#[\w-]+/g) || []).map(t => t.slice(1).toLowerCase())
}

function renderInlineTags(desc) {
  return desc.replace(/(#[\w-]+)/g, '<span class="inline-tag">$1</span>')
}

function formatAge(d) {
  if (d === 0) return 'today'
  if (d === 1) return '1d ago'
  if (d < 7)   return `${d}d ago`
  return `${Math.floor(d / 7)}w ago`
}

// ─── Computed ─────────────────────────────────────────────────────────────────

const allTagCounts = computed(() => {
  const t = {}
  cases.value.forEach(c => extractTags(c.desc).forEach(tag => { t[tag] = (t[tag] || 0) + 1 }))
  return Object.entries(t).sort((a, b) => b[1] - a[1])
})

const TOP_TAGS = 8
const topTags      = computed(() => allTagCounts.value.slice(0, TOP_TAGS))
const extraTagCount = computed(() => Math.max(0, allTagCounts.value.length - TOP_TAGS))

const filtered = computed(() => {
  const q = searchQuery.value.toLowerCase().trim()
  const hashMatch = q.startsWith('#') ? q.slice(1) : null

  return cases.value.filter(c => {
    const tags = extractTags(c.desc)
    if (activeTag.value && !tags.includes(activeTag.value)) return false
    if (activeSys.value && c.system !== activeSys.value)    return false
    if (hashMatch && !tags.some(t => t.startsWith(hashMatch))) return false
    if (q && !q.startsWith('#') && !c.desc.toLowerCase().includes(q) && !c.id.toLowerCase().includes(q)) return false
    return true
  })
})

// ─── Actions ──────────────────────────────────────────────────────────────────
async function fetchCases() {
  try {
    loading.value = true
    const response = await fetch('https://medibyte.app/api/wet-read/cases')
    const data = await response.json()
    if (data.success) {
      cases.value = data.cases
    }
  } catch (err) {
    showToast('Failed to load cases', 'error')
  } finally {
    loading.value = false
  }
}

function handleCopy(id) {
  navigator.clipboard.writeText(id).catch(() => {})
  flashingId.value = id
  setTimeout(() => { flashingId.value = null }, 1200)
}

function startRLTimer() {
  rlSeconds.value = 10
  rlInterval = setInterval(() => {
    rlSeconds.value--
    if (rlSeconds.value <= 0) {
      clearInterval(rlInterval)
      rlSeconds.value = 0
    }
  }, 1000)
}
async function submitCase() {
  if (rlSeconds.value > 0) return

  const { id, system, desc } = form.value
  if (!id.trim() || !system || !desc.trim()) {
    showToast('Please fill in all three fields', 'error')
    return
  }

  try {
    const response = await fetch('https://medibyte.app/api/wet-read/submit-case', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ 
          accession: id.trim().toUpperCase(), 
        system, 
        description: desc.trim() 
      })
    })

    if (!response.ok) throw new Error('Submission failed')

    form.value = { id: '', system: '', desc: '' }
    submitOpen.value = true
    startRLTimer()
    showToast('Case submitted! Will appear once reviewed', 'success')
    await fetchCases()
  } catch (err) {
    showToast('Submission failed. Please try again', 'error')
  }
}

onUnmounted(() => {
    if (rlInterval)
        clearInterval(rlInterval)
},

onMounted(() => {
  fetchCases()
})

)
</script>

<style scoped>
.module {
  background: var(--bg-surface);
  border: 1px solid var(--border);
  border-radius: 10px;
  overflow: hidden;
  font-family: 'JetBrains Mono', monospace;
  color: var(--text-primary);
}

/* ── Header ── */
.module-header {
  padding: 14px 18px 12px;
  border-bottom: 1px solid var(--border);
  display: flex;
  align-items: center;
  gap: 16px;
  flex-wrap: wrap;
}
.module-label {
  font-size: 10px;
  letter-spacing: .12em;
  color: var(--accent);
  text-transform: uppercase;
  font-weight: 600;
}
.module-title {
  font-size: 15px;
  color: var(--text-primary);
  font-family: sans-serif;
  font-weight: 400;
}
.toolbar {
  display: grid;
  grid-template-columns: 4fr 0.5fr 0.5fr 1.5fr;
  align-items: center;
  gap: 8px;
  width:100%;
}

/* ── Search ── */
.search-wrap { position: relative; }
.search-wrap i {
  position: absolute;
  left: 9px;
  top: 50%;
  transform: translateY(-50%);
  color: var(--text-muted);
  font-size: 14px;
  pointer-events: none;
}
.search-input {
  background: var(--bg-raised);
  border: 1px solid var(--border);
  border-radius: 6px;
  color: var(--text-primary);
  font-size: 12px;
  font-family: monospace;
  padding: 6px 10px 6px 28px;
  width: 100%;
  outline: none;
  transition: border-color .15s;
}
.search-input:focus { border-color: var(--accent); }
.search-input::placeholder { color: var(--text-dim); }

/* ── View toggle ── */
.view-toggle {
  display: flex;
  border: 1px solid var(--border);
  border-radius: 6px;
  overflow: hidden;
}
.view-btn {
  background: var(--bg-raised);
  border: none;
  color: var(--text-muted);
  padding: 6px 9px;
  cursor: pointer;
  font-size: 14px;
  transition: all .15s;
}
.view-btn.active {
  background: var(--accent-glow);
  color: var(--accent);
}

/* ── Add button ── */
.add-btn {
  background: var(--accent-glow);
  border: 1px solid var(--accent);
  border-radius: 6px;
  color: var(--accent);
  font-size: 11px;
  font-family: monospace;
  padding: 6px 12px;
  cursor: pointer;
  letter-spacing: .06em;
  transition: all .15s;
  display: flex;
  align-items: center;
  gap: 5px;
}
.add-btn:hover { background: rgba(74,158,166,.22); }

/* ── Submit panel ── */
.submit-panel {
  display: none;
  padding: 16px 18px;
  border-bottom: 1px solid var(--border);
  background: var(--bg-raised);
}
.submit-panel.open { display: block; }
.submit-grid {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 10px;
  margin-bottom: 10px;
}
.field-full { grid-column: 1 / -1; }
.field-label {
  font-size: 9px;
  letter-spacing: .1em;
  text-transform: uppercase;
  color: var(--text-dim);
  margin-bottom: 5px;
  font-weight: 600;
}
.field-input {
  background: var(--bg-surface);
  border: 1px solid var(--border);
  border-radius: 6px;
  color: var(--text-primary);
  font-size: 12px;
  font-family: monospace;
  padding: 8px 10px;
  width: 100%;
  outline: none;
  transition: border-color .15s;
}
.field-input:focus { border-color: var(--accent); }
.field-input::placeholder { color: var(--text-dim); }
select.field-input option { background: var(--bg-surface); }
.hint {
  font-size: 10px;
  color: var(--text-dim);
  margin-top: 5px;
}
.pending-note {
  font-size: 10px;
  color: var(--text-muted);
  margin-top: 8px;
  padding: 7px 10px;
  border: 1px solid var(--border);
  border-radius: 6px;
  line-height: 1.5;
}
.pending-note i { color: var(--accent); vertical-align: -2px; margin-right: 4px; }
.submit-row {
  display: flex;
  gap: 8px;
  justify-content: space-between;
  align-items: center;
  margin-top: 10px;
}
.rl-note { font-size: 10px; color: var(--text-dim); }
.rl-timer { color: var(--accent); }
.submit-actions { display: flex; gap: 8px; }
.cancel-btn {
  background: transparent;
  border: 1px solid var(--border);
  border-radius: 6px;
  color: var(--text-muted);
  font-size: 11px;
  font-family: monospace;
  padding: 7px 14px;
  cursor: pointer;
}
.cancel-btn:hover { border-color: var(--border-light); color: var(--text-primary); }
.submit-go {
  background: var(--accent-glow);
  border: 1px solid var(--accent);
  border-radius: 6px;
  color: var(--accent);
  font-size: 11px;
  font-family: monospace;
  padding: 7px 16px;
  cursor: pointer;
  letter-spacing: .06em;
}
.submit-go:disabled { opacity: .4; cursor: not-allowed; }

/* ── Tag bar ── */
.tag-bar {
  padding: 9px 18px;
  border-bottom: 1px solid var(--border);
  display: flex;
  gap: 6px;
  flex-wrap: wrap;
  align-items: center;
  min-height: 38px;
}
.tag-label {
  font-size: 9px;
  color: var(--text-dim);
  text-transform: uppercase;
  letter-spacing: .1em;
  white-space: nowrap;
}
.tag-pill {
  background: var(--bg-raised);
  border: 1px solid var(--border);
  border-radius: 20px;
  color: var(--text-muted);
  font-size: 10px;
  font-family: monospace;
  padding: 3px 10px;
  cursor: pointer;
  transition: all .15s;
  letter-spacing: .04em;
  white-space: nowrap;
}
.tag-pill:hover { border-color: var(--accent); color: var(--accent); }
.tag-pill.active { background: var(--accent-glow); border-color: var(--accent); color: var(--accent); }
.tag-more { font-size: 10px; color: var(--text-dim); padding: 3px 8px; }
.divider-v {
  width: 1px;
  background: var(--border);
  height: 14px;
  margin: 0 2px;
  flex-shrink: 0;
}
.sys-pill {
  font-size: 9px;
  padding: 3px 9px;
  border-radius: 20px;
  font-weight: 600;
  letter-spacing: .06em;
  cursor: pointer;
  border: 1px solid;
  transition: all .15s;
  white-space: nowrap;
}

/* ── Stats bar ── */
.stats-bar { display: flex; border-bottom: 1px solid var(--border); }
.stat { padding: 8px 18px; border-right: 1px solid var(--border); }
.stat:last-child { border-right: none; }
.stat-n { font-size: 16px; color: var(--text-primary); font-family: monospace; font-weight: 600; }
.stat-l { font-size: 9px; color: var(--text-dim); text-transform: uppercase; letter-spacing: .1em; }

/* ── Table (list view) ── */
.table-view { width: 100%; border-collapse: collapse; }
.table-view thead th {
  font-size: 9px;
  letter-spacing: .1em;
  text-transform: uppercase;
  color: var(--text-dim);
  padding: 8px 18px;
  text-align: left;
  border-bottom: 1px solid var(--border);
  font-weight: 600;
}
.table-view tbody tr {
  border-bottom: 1px solid var(--border);
  cursor: pointer;
  transition: background .1s;
}
.table-view tbody tr:hover {
  background: var(--bg-hover);
}
.table-view tbody tr.flash-copy {
  background: rgba(58, 158, 106, 0.15);
  transition: none !important;
}
.table-view tbody tr.flash-copy:hover {
  background: rgba(58, 158, 106, 0.15);
}

.table-view tbody tr:last-child { border-bottom: none; }
.table-view td { padding: 9px 18px; font-size: 12px; font-family: monospace; vertical-align: middle; }



.acc-cell { display: flex; align-items: center; gap: 6px; }
.acc-text  { color: var(--text-muted); font-size: 11px; white-space: nowrap; transition: color .15s; }
.copy-icon { font-size: 11px; color: var(--text-dim); transition: color .15s; }
.acc-ok    { color: var(--copy-ok) !important; }

.td-desc {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, sans-serif;

    color: var(--text-primary); line-height: 1.4; }
.td-age  { color: var(--text-dim); white-space: nowrap; font-size: 11px; text-align: right; }
.td-system { white-space: nowrap; }

/* ── System badge ── */
.sys-badge {
  font-size: 9px;
  padding: 2px 7px;
  border-radius: 4px;
  font-weight: 600;
  letter-spacing: .06em;
  text-transform: uppercase;
  border: 1px solid;
}

/* ── Card view ── */
.card-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(270px, 1fr));
  gap: 10px;
  padding: 14px;
}
.case-card {
  background: var(--bg-raised);
  border: 1px solid var(--border);
  border-radius: 8px;
  padding: 12px 14px;
  cursor: pointer;
  transition: background .1s, border-color .1s;
}
.case-card:hover {
  background: var(--bg-hover);
  border-color: var(--border-light);
}
.case-card.flash-copy {
  background: rgba(58, 158, 106, 0.15);
  border-color: var(--copy-ok) !important;
  transition: none !important;
}
.case-card.flash-copy:hover {
  background: rgba(58, 158, 106, 0.15);
  border-color: var(--copy-ok) !important;
}
.card-top { display: flex; justify-content: space-between; align-items: center; margin-bottom: 8px; }
.card-id-wrap { display: flex; align-items: center; gap: 5px; }
.card-id-text { 
  font-size: 11px; 
  color: var(--text-muted); 
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, sans-serif;
}
.card-age { font-size: 10px; color: var(--text-dim); }
.card-desc { font-size: 12px; color: var(--text-primary); line-height: 1.5; margin: 8px 0;
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, sans-serif;
           }
.card-footer { display: flex; gap: 6px; align-items: center; }

/* ── Inline tag highlight ── */
:deep(.inline-tag) { color: var(--accent); font-size: 10px; opacity: .75; }
.inline-tag { color: var(--accent); font-size: 10px; opacity: .75; }

/* ── Empty state ── */
.empty { padding: 40px; text-align: center; color: var(--text-dim); font-size: 13px; }
</style>
