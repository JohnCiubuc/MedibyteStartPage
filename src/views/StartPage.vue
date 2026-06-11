<template>
  <div class="page">
    <div class="scanlines" aria-hidden="true" />
    <main class="main">
      <h1 class="title">Medibyte Start Page</h1>
      <p class="version">
        <span class="pip" aria-hidden="true" />
        <button class="learn-more" @click="showVersion = !showVersion">v1.0.1</button>
      </p>
      <div v-if="showVersion" class="help-panel">
        <p class="help-title">v1.0.1 — What's new</p>
        <ul class="version-list">
          <li>Links open in-page rather than in a new tab</li>
          <li>Fixed spacing and size of tiles</li>
          <li>Increased TileGroups to show 8 tiles in a row (from 5)</li>
          <li>Added new Loose Bangs: OpenEvidence, PubMed, Google Scholar</li>
          <li>Added CT Cirrhotic Abdomen, CTA Lower Extremity, CT Ankle and Foot</li>
        </ul>
      </div>
      <SearchBar />
      <QuickAccess :tiles="soloTiles" />
      <TileGroup eyebrow="Reference Library" title="eAnatomy" :tiles="eAnatomyTiles" />
      <TileGroup eyebrow="Rad Assistant Quick Links" title="Rad Assistant" :tiles="radAssistantTiles" />
    </main>
    <AppFooter />
  </div>
</template>

<script setup>
import { ref } from 'vue'
import SearchBar from '@/components/SearchBar.vue'
import TileGroup from '@/components/TileGroup.vue'
import QuickAccess from '@/components/QuickAccess.vue'
import AppFooter from '@/components/AppFooter.vue'

const showVersion = ref(false)
const eAnatomyTiles = [
  { label: 'MRI Brain', icon: 'https://cdn1.imaios.com/i/images/4/2/6/0/310624-3-eng-GB/f9296ee2c558-brain3dmri.png?q=75&w=230&s=6419caa48600b24d59e4ff706f7222d3', url: 'https://www.imaios.com/en/e-anatomy/brain/mri-brain' },
  { label: 'CT Brain', icon: 'https://cdn1.imaios.com/i/images/5/8/5/0/310585-3-eng-GB/0a8f1dd62341-brain-ct.png?q=75&w=230&s=e2205d9962bc65dab16ad83e39523668', url: 'https://www.imaios.com/en/e-anatomy/brain/ct-brain' },
  { label: 'CT Head and Neck', icon: 'https://cdn1.imaios.com/i/images/2/9/7/1/131792-4-eng-GB/caebb438ec94-Head-Neck-CT.png?q=75&w=230&s=ff1fb7b910c5e632425be1b464c46e63', url: 'https://www.imaios.com/en/e-anatomy/head-and-neck/ct-head-and-neck' },
  { label: 'CT Face', icon: 'https://cdn1.imaios.com/i/images/6/8/7/1/131786-5-eng-GB/e4a720e3e88b-face.png?q=75&w=230&s=abfc4f7f9c056c02ed7ebf8e8f5863ee', url: 'https://www.imaios.com/en/e-anatomy/head-and-neck/ct-face' },
  { label: 'CT Temporal Bone', icon: 'https://cdn1.imaios.com/i/images/9/8/8/0/310889-3-eng-GB/20f6136a1276-rocher.png?q=75&w=230&s=b81e6b55fef2c059926768851a4e7555', url: 'https://www.imaios.com/en/e-anatomy/head-and-neck/ct-temporal-bone' },
  { label: 'MRI Cervical Spine', icon: 'https://cdn1.imaios.com/i/images/5/3/7/1/131735-3-eng-GB/ce0f1ce03657-cervical-spine-mri.png?q=75&w=230&s=1fa324b450d74a2b19a517f45ab2f5d3', url: 'https://www.imaios.com/en/e-anatomy/spine/mri-cervical-spine' },
  { label: 'MRI Lumbar Spine', icon: 'https://cdn1.imaios.com/i/images/7/8/0/0/460087-2-eng-GB/95c684afdf4e-lumbar-spine-mri.png?q=75&w=230&s=ff4c05c22f7cf136b37185de1a7db329', url: 'https://www.imaios.com/en/e-anatomy/spine/mri-lumbar-spine' },
  { label: 'CT Lumbar Spine', icon: 'https://cdn1.imaios.com/i/images/8/3/7/1/131738-3-eng-GB/080c9bb3cb09-lumbar-spine-ct.png?q=75&w=230&s=51f61d0d7765ccb3c5bd461d51974a8c', url: 'https://www.imaios.com/en/e-anatomy/spine/ct-lumbar-spine' },
  { label: 'CT Body Lymph Nodes', icon: 'https://cdn1.imaios.com/i/images/3/5/4/0/310453-3-eng-GB/9b48c06243a6-aires-radiotherapie.png?q=75&w=230&s=1f7bcac75a4a0a7563ace0fa47a11c56', url: 'https://www.imaios.com/en/e-anatomy/whole-body/ct-body-lymph-nodes' },
  { label: 'CT Chest', icon: 'https://cdn1.imaios.com/i/images/9/5/1/1/311159-5-eng-GB/686203b08da4-thorax.png?q=75&w=230&s=3238dc3c9b34c0477182df07e6389490', url: 'https://www.imaios.com/en/e-anatomy/thorax/ct-axial-chest' },
  { label: 'MRCP', icon: 'https://cdn1.imaios.com/i/images/5/1/3/1/311315-4-eng-GB/220cb5a0c48b-cholangiopancreatography-mr.png?q=75&w=230&s=72b7f3a4822055d81645dcc72e79dcc7', url: 'https://www.imaios.com/en/e-anatomy/abdomen-and-pelvis/magnetic-resonance-cholangiopancreatography' },
  { label: 'CT Abdomen and Pelvis', icon: 'https://cdn1.imaios.com/i/images/3/9/3/1/311393-4-eng-GB/1cff6e56270a-abdomen.png?q=75&w=230&s=3d7cf3818d92511c44812c86b0d7a3b7', url: 'https://www.imaios.com/en/e-anatomy/abdomen-and-pelvis/ct-axial-male-abdomen-and-pelvis' },
  { label: 'CT Cirrhotic Abdomen and Pelvis', icon: 'https://medibyte.app/sick_abd.jpg', url: 'https://www.imaios.com/en/e-anatomy/abdomen-and-pelvis/ct-peritoneal-cavity' },
  { label: 'Radiograph Upper Extremity', icon: 'https://cdn1.imaios.com/i/images/0/6/5/0/310560-4-eng-GB/afc5ddc0b3b9-ms-radios.png?q=75&w=230&s=3f30892607555d5a4a0382f0e512e5c3', url: 'https://www.imaios.com/en/e-anatomy/upper-limb/radiography-upper-extremity' },
  { label: 'Radiograph Lower Extremity', icon: 'https://cdn1.imaios.com/i/images/1/1/7/1/131711-4-eng-GB/04ec3cbb1fae-membre-inferieur-radiographies.png?q=75&w=230&s=34083eee34a73622cf8903ed283c1500', url: 'https://www.imaios.com/en/e-anatomy/lower-limb/radiography-lower-extremity' },
  { label: 'CTA Lower Extremity', icon: 'https://cdn1.imaios.com/i/images/2/3/8/3/12723832-2-eng-GB/5f2c8a3e5e4f-lower-limb-cta.png?q=75&w=230&s=243825fed41448e7971262f9a49b23cf', url: 'https://www.imaios.com/en/e-anatomy/lower-limb/lower-limb-cta' },
  { label: 'CT Ankle and Foot', icon: 'https://cdn1.imaios.com/i/images/2/1/1/5/14435112-1-eng-GB/ca1bfee21c6b-ankle-foot-ct.png?q=75&w=230&s=6277c70aed8d1ca8dcde08c65c5d1351', url: 'https://www.imaios.com/en/e-anatomy/lower-limb/ankle-and-foot-ct' },
]

const radAssistantTiles = [
  { label: 'Peds Elbow Fractures', icon: 'https://radiologyassistant.nl/assets/elbow-fractures-in-children/a50979768b5d3a_critoe2.jpg', url: 'https://radiologyassistant.nl/pediatrics/hip/fractures-in-children-1' },
  { label: 'TI-RADS', icon: 'https://radiologyassistant.nl/assets/_1a-tab-tirads-landscape.jpg', url: 'https://radiologyassistant.nl/head-neck/ti-rads/ti-rads' },
  { label: 'Thoracolumbar Fractures', icon: 'https://radiologyassistant.nl/assets/1-tek-intro-c-1709283624.jpg', url: 'https://radiologyassistant.nl/musculoskeletal/spine/ao-classification' },
]

const soloTiles = [
  { label: 'MSK MRI', icon: 'https://freitasrad.net/images/mainImage.jpg', url: 'https://freitasrad.net/' },
  { label: 'Rad-Call', icon: 'https://external-content.duckduckgo.com/ip3/rad-call.com.ico', url: 'https://rad-call.com/app.html' },
  { label: '9GAG', icon: 'https://cdn-icons-png.flaticon.com/512/2111/2111316.png', url: 'https://9gag.com' },
  { label: 'OpenEvidence', icon: 'https://play-lh.googleusercontent.com/Mjei293xYv3eexylcUY-9AM33LUElOPHdn9BOuUD4mjY5pcnL0-8nppNdTPJptgJJczT', url: 'https://www.openevidence.com/' },
  { label: 'Qualified Health', icon: 'https://www.finsmes.com/wp-content/uploads/2026/03/Qualified-Health.jpeg', url: 'https://chat.qualifiedhealthai.com/' },
  { label: 'Outlook', icon: 'https://upload.wikimedia.org/wikipedia/commons/thumb/c/cc/Microsoft_Outlook_Icon_%282025%E2%80%93present%29.svg/1280px-Microsoft_Outlook_Icon_%282025%E2%80%93present%29.svg.png', url: 'https://outlook.office365.com/mail/' },
]
</script>

<style scoped>
.page {
  position: relative;
  min-height: 100vh;
  display: flex;
  flex-direction: column;
}

.scanlines {
  position: fixed;
  inset: 0;
  background-image: repeating-linear-gradient(0deg, transparent, transparent 2px, rgba(0, 0, 0, 0.045) 2px, rgba(0, 0, 0, 0.045) 4px);
  pointer-events: none;
  z-index: 0;
}

.main {
  position: relative;
  z-index: 1;
  flex: 1;
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: var(--main-padding);
  width: 100%;
  max-width: 800px;
  margin: 0 auto;
  gap: var(--main-gap);
}

.title {
  font-size: 26px;
  font-weight: 600;
  letter-spacing: -0.01em;
  color: var(--text-primary);
  line-height: 1;
}

.version {
  margin-top: 7px;
  font-size: 11px;
  font-family: 'JetBrains Mono', monospace;
  color: var(--text-dim);
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 6px;
}

.pip {
  display: inline-block;
  width: 6px;
  height: 6px;
  border-radius: 50%;
  background: var(--accent);
  opacity: 0.7;
  flex-shrink: 0;
}

@media (max-height: 800px) {
  .title { font-size: 20px; }
  .version { margin-top: 4px; }
}

@media (max-height: 620px) {
  .title { font-size: 17px; }
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
.version-list {
  list-style: none;
  margin: 0;
  padding: 0;
  font-size: 12px;
}

.version-list li {
  color: var(--text-primary);
  padding: 0;
  font-family: 'JetBrains Mono', monospace;
}

.version-list li + li {
  padding-top: 6px;
}

.version-list li::before {
  content: '—';
  color: var(--accent);
  margin-right: 8px;
}
</style>
