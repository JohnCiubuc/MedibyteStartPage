<template>
  <div class="phone-directory">
    <div class="directory-header">
      <h1 class="directory-title">Radiology Phone Directory</h1>
      <input
        v-model="search"
        class="search-input"
        placeholder="Search name or number…"
        autofocus
      />
    </div>
    <div class="groups-grid">
      <section v-for="group in filteredGroups" :key="group.title + group.eyebrow" class="card">
        <span class="section-eyebrow">{{ group.eyebrow }}</span>
        <h2 class="card-title">{{ group.title }}</h2>
        <ul class="entry-list">
          <li v-for="(e, i) in group.entries" :key="i" class="entry-row">
            <span class="entry-label">{{ e.label }}</span>
            <span class="entry-numbers">
              <span v-if="e.extCell" class="entry-ext">{{ e.extCell }}</span>
              <span v-if="e.number" class="entry-number">{{ e.number }}</span>
            </span>
          </li>
        </ul>
      </section>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'

const search = ref('')

const allGroups = [
  {
    eyebrow: 'UHS — Modalities / Techs',
    title: 'CT',
    entries: [
      { label: 'UH Tech', number: '8-2963' },
      { label: 'UH Tech', number: '8-2974' },
      { label: 'Recovery / CT Holding', number: '8-3454' },
      { label: 'Recovery / CT Holding', number: '8-8673' },
      { label: 'Recovery / CT Holding', number: '8-8671' },
      { label: 'Sky Tower 6th', number: '3-4776' },
      { label: 'Sky Tower 6th', number: '3-4777' },
      { label: 'Sky Tower 6th', number: '3-4778' },
      { label: 'ED CT 1', number: '3-4579' },
      { label: 'ED CT 2', number: '3-4578' },
      { label: 'Central Monitor ED', number: '3-4577' },
      { label: 'Central Monitor ED', number: '3-4576' },
      { label: 'WC CT', number: '5-0046' },
    ],
  },
  {
    eyebrow: 'UHS — Modalities / Techs',
    title: 'MR',
    entries: [
      { label: 'Horizon 1.5T', number: '8-4178' },
      { label: 'Horizon 3T', number: '8-4172' },
      { label: 'Horizon MRI Nurse', number: '8-1560' },
      { label: 'Sky Tower MR', number: '3-1223' },
      { label: 'Sky Tower MR Nurse', number: '3-1224' },
    ],
  },
  {
    eyebrow: 'UHS — Modalities / Techs',
    title: 'U/S',
    entries: [
      { label: 'UH Primary', number: '8-2934' },
      { label: 'US Tech Portable', number: '3-4591' },
      { label: 'US Tech Portable', number: '3-4592' },
      { label: 'US Tech Portable', number: '3-4593' },
      { label: 'Express Med Pavilion', number: '8-8545' },
    ],
  },
  {
    eyebrow: 'UHS — Modalities / Techs',
    title: 'X-Ray',
    entries: [
      { label: 'UH Routines', number: '8-2733' },
      { label: 'UH Routines', number: '8-2743' },
      { label: 'Fluoro', number: '8-2737' },
      { label: 'ED', number: '3-4580' },
      { label: 'ED', number: '3-4581' },
      { label: 'ED', number: '3-4582' },
      { label: 'Sky Tower Portables', number: '3-4594' },
      { label: 'Sky Tower Portables', number: '3-4595' },
      { label: 'Sky Tower Portables', number: '3-4596' },
      { label: 'Express Med Pavilion', number: '8-2146' },
      { label: 'WC Pedi XR', number: '5-4516' },
    ],
  },
  {
    eyebrow: 'UHS — Reading Rooms',
    title: 'Reading Rooms',
    entries: [
      { label: 'ED', number: '3-4583' },
      { label: 'ED', number: '3-4584' },
      { label: 'ED', number: '3-4585' },
      { label: 'ED', number: '3-4586' },
      { label: 'Neuro', number: '8-0260' },
      { label: 'Neuro', number: '8-0294' },
      { label: 'Neuro', number: '8-0295' },
      { label: 'Neuro', number: '8-0299' },
      { label: 'MSK', number: '8-0596' },
      { label: 'MSK / Dr. Bean', number: '8-0597' },
      { label: 'MSK', number: '8-0598' },
      { label: 'Dr. Loredo', number: '8-4425' },
      { label: 'Chest', number: '8-2137' },
      { label: 'Chest', number: '8-2493' },
      { label: 'Chest', number: '8-2763' },
      { label: 'Pedi', number: '8-2765' },
      { label: 'Fluoro', number: '8-8831' },
      { label: 'Body CT', number: '8-0452' },
      { label: 'Body CT', number: '8-2966' },
      { label: 'Body U/S', number: '8-0569' },
      { label: 'Body MR', number: '8-2414' },
      { label: 'Body MR', number: '8-4171' },
      { label: 'Nuc Medicine', number: '8-2942' },
      { label: 'Nuc Medicine', number: '8-2764' },
      { label: 'Nuc Medicine Tech', number: '8-2936' },
      { label: 'Mammo CTRC', number: '644-8857' },
      { label: 'Mammo CTRC', number: '644-8859' },
      { label: 'UH Angio Reading', number: '8-8468' },
      { label: 'UH Angio Reading', number: '8-0628' },
      { label: 'Hallway', number: '8-2748' },
      { label: 'Room #3', number: '8-2612' },
    ],
  },
  {
    eyebrow: 'UHS — Procedures',
    title: 'Procedures',
    entries: [
      { label: 'Radiology Recovery', number: '8-8673' },
      { label: 'Radiology Recovery', number: '8-8838' },
      { label: 'Radiology Recovery', number: '8-0354' },
      { label: 'Body Proc Wall', number: '8-2799' },
      { label: 'UH Body IR Reading', number: '8-8399' },
      { label: 'UH Fluoro RR', number: '8-8831' },
      { label: 'UH Fluoro Tech', number: '8-2737' },
      { label: 'UH Angio Reading', number: '8-8468' },
      { label: 'UH Angio Reading', number: '8-0628' },
      { label: 'Chief Tech', number: '8-0282' },
      { label: 'IR Clinic', number: '8-0420' },
      { label: 'Hallway', number: '8-2748' },
      { label: 'Room #3', number: '8-2612' },
    ],
  },
  {
    eyebrow: 'UHS — Procedures',
    title: 'Procedures',
    entries: [
      { label: 'Radiology Recovery', number: '8-8673' },
      { label: 'Radiology Recovery', number: '8-8838' },
      { label: 'Radiology Recovery', number: '8-0354' },
      { label: 'Body Proc Wall', number: '8-2799' },
      { label: 'UH Body IR Reading', number: '8-8399' },
      { label: 'UH Fluoro RR', number: '8-8831' },
      { label: 'UH Fluoro Tech', number: '8-2737' },
      { label: 'UH Angio Reading', number: '8-8468' },
      { label: 'UH Angio Reading', number: '8-0628' },
      { label: 'Chief Tech', number: '8-0282' },
      { label: 'IR Clinic', number: '8-0420' },
      { label: 'Hallway', number: '8-2748' },
      { label: 'Room #3', number: '8-2612' },
    ],
  },
  {
    eyebrow: 'RGB',
    title: 'RGB',
    entries: [
      { label: 'RGB CT 1', number: '8-6572' },
      { label: 'RGB CT 2', number: '8-3589' },
    ],
  },
  {
    eyebrow: 'MARC — Trauma / ED',
    title: 'Trauma / ED',
    entries: [
      { label: 'EM-1', number: '3-0162' },
      { label: 'EM-2', number: '3-0163' },
      { label: 'EM-3', number: '3-0164' },
      { label: 'Fast Track', number: '3-0170' },
      { label: 'Trauma Pit Boss', number: '8-8728' },
      { label: 'OB Triage', number: '8-2413' },
      { label: 'OB Triage', number: '8-4390' },
      { label: 'Gyn Triage', number: '8-1548' },
      { label: 'CODE ANGIO', number: '8-2222' },
      { label: 'Angio Call Pager', number: '203-8181' },
      { label: 'Body Call Pager', number: '203-3622' },
      { label: 'Rad Nurse Portable', number: '8-8148' },
    ],
  },
  {
    eyebrow: 'MARC — Help & Info',
    title: 'Help Desk & Info',
    entries: [
      { label: 'Radiology Help Desk', number: '8-8532' },
      { label: 'UHS Computer Help Desk', number: '644-0119' },
      { label: 'UHS Computer Text Support', number: '8-4059' },
      { label: 'Long Distance Code', number: '2611735' },
    ],
  },
  {
    eyebrow: 'UH Radiology — SW Clinics',
    title: 'SW Clinics',
    entries: [
      { label: 'Cheryl', number: '567-3448' },
      { label: 'Angela', number: '567-6488' },
      { label: 'Gladys (Body)', number: '567-6470' },
      { label: 'Jessica (Angio)', number: '567-5564' },
      { label: 'SW', number: '8-5116' },
      { label: 'SE', number: '8-5515' },
      { label: 'North', number: '8-0835' },
      { label: 'NW', number: '8-9480' },
      { label: 'Pavilion', number: '8-2146' },
      { label: 'UCCH', number: '8-7694' },
    ],
  },
{
    eyebrow: 'UT Health',
    title: 'Practice Directory',
    entries: [
      { label: 'Audiology', extCell: '6B', number: '210-450-9950' },
      { label: 'Cardiology', extCell: '3B', number: '210-450-4888' },
      { label: 'Cardiothoracic Surgery', extCell: '3B', number: '210-450-0999' },
      { label: 'Cranial Remolding', extCell: '7A', number: '210-450-9064' },
      { label: 'Customer Service & Billing', extCell: '1', number: '210-450-6330' },
      { label: 'Day Surgery Center - UHS', extCell: '2', number: '210-644-9300' },
      { label: 'Diabetes Center', extCell: '3A', number: '210-450-9800' },
      { label: 'Ear, Nose, and Throat/Otolaryngology', extCell: '6B', number: '210-450-9950' },
      { label: 'Endocrinology', extCell: '4A', number: '210-450-9800' },
      { label: 'Family Medicine', extCell: '1', number: '210-450-9100' },
      { label: 'Fertility Center', extCell: '5A', number: '210-450-9500' },
      { label: 'Gastroenterology', extCell: '4A', number: '210-450-9800' },
      { label: 'General Surgery', extCell: '4A', number: '210-450-9200' },
      { label: 'Geriatric Medicine', extCell: '1', number: '210-450-9020' },
      { label: 'H-E-B Pharmacy', extCell: '1', number: '210-593-0291' },
      { label: 'Heart Station - UHS', extCell: '3B', number: '210-644-3278' },
      { label: 'Infectious Diseases', extCell: '4A', number: '210-450-9800' },
      { label: 'Internal Medicine', extCell: '1', number: '210-450-9020' },
      { label: 'Maternal Fetal Medicine', extCell: '5', number: '210-450-9500' },
      { label: 'Medical Records', extCell: '1', number: '210-450-9760' },
      { label: 'Nephrology', extCell: '4A', number: '210-450-9800' },
      { label: 'Neurology & EMG', extCell: '8', number: '210-450-9700' },
      { label: 'Neurosurgery', extCell: '7', number: '210-450-9060' },
      { label: 'Obstetrics & Gynecology', extCell: '5', number: '210-450-9500' },
      { label: 'Ophthalmology/Vision Care', extCell: '6A,7', number: '210-450-9400' },
      { label: 'Orthopaedics', extCell: '3C', number: '210-450-9300' },
      { label: 'Physical Therapy', extCell: '3A', number: '210-450-9680' },
      { label: 'Plastic & Reconstructive Surgery', extCell: '4A', number: '210-450-9220' },
      { label: 'Primary Care', extCell: '1', number: '210-450-9100' },
      { label: 'Podiatry', extCell: '3C', number: '210-450-9300' },
      { label: 'Pulmonary Medicine & Pulmonary Lab', extCell: '4A', number: '210-450-9800' },
      { label: 'UT Health Lab Services', extCell: '1', number: '210-450-9946' },
      { label: 'Radiology', extCell: '3D', number: '210-450-6000' },
      { label: 'Rheumatology', extCell: '4A', number: '210-450-9800' },
      { label: 'Speech Therapy', extCell: '6B', number: '210-450-9950' },
      { label: 'Sports Medicine', extCell: '3C', number: '210-450-9300' },
      { label: 'UHS Lab Services', extCell: '1', number: '210-450-9945' },
      { label: 'Urology', extCell: '4B', number: '210-450-9600' },
      { label: 'Vascular & Endovascular Surgery', extCell: '4A', number: '210-450-9888' },
    ],
  },
  {
    eyebrow: 'UT Health',
    title: 'Help Desks',
    entries: [
      { label: 'QC Desk — Critical Results (7am–7pm)', number: '210-450-9757' },
      { label: 'QC Desk — After Hours Answering Service', number: '210-450-9000' },
      { label: 'IT Help Desk — EPIC Support (Opt 1)', number: '210-450-4800' },
      { label: 'IT Help Desk — Computer Support (Opt 2)', number: '210-450-4800' },
      { label: 'IT Help Desk — PACS/RIS/Powerscribe 360 (Opt 3)', number: '210-450-4800' },
      { label: 'Radiology Help Desk', number: '210-450-9757' },
      { label: 'Evening & Weekend Answering Service', extCell: 'if MARC down', number: '210-450-9000' },
    ],
  },
  {
    eyebrow: 'UT Health - HC Hill Country',
    title: 'Imaging Extensions',
    entries: [
      { label: 'XR', number: '06827' },
      { label: 'MR', number: '06824' },
      { label: 'CT', number: '06826' },
      { label: 'US', number: '06825' },
      { label: 'Reading Room', number: '06828' },
    ],
  },
  {
    eyebrow: 'UT Health - MARC',
    title: 'Imaging Extensions',
    entries: [
      { label: 'XR', number: '09757' },
      { label: 'MR', number: '09747' },
      { label: 'CT', number: '09749' },
      { label: 'US', number: '09752' },
      { label: 'Reading Room', number: '09745' },
      { label: 'NM', number: '09033' },
    ],
  },
  {
    eyebrow: 'UT Health - KSP',
    title: 'Imaging Extensions',
    entries: [
      { label: 'XR', number: '79071' },
      { label: 'MR', number: '79073' },
      { label: 'CT', number: '79077' },
      { label: 'US', number: '79066' },
      { label: 'Reading Room', number: '79183' },
    ],
  },
]

const filteredGroups = computed(() => {
  const q = search.value.toLowerCase().trim()
  if (!q) return allGroups
  return allGroups
    .map(g => ({
      ...g,
      entries: g.entries.filter(
        e =>
          e.label.toLowerCase().includes(q) ||
          e.number.includes(q) ||
          (e.extCell && e.extCell.includes(q))
      ),
    }))
    .filter(
      g =>
        g.entries.length ||
        g.title.toLowerCase().includes(q) ||
        g.eyebrow.toLowerCase().includes(q)
    )
})
</script>

<style scoped>
.phone-directory {
  padding: 20px;
  font-family: 'Inter', sans-serif;
}

.directory-header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  margin-bottom: 20px;
  gap: 16px;
  flex-wrap: wrap;
}

.directory-title {
  font-size: 18px;
  font-weight: 600;
  color: var(--text-primary);
  margin: 0;
}

.search-input {
  background: var(--bg-raised);
  border: 1px solid var(--border);
  color: var(--text-primary);
  font-family: 'Inter', sans-serif;
  font-size: 13px;
  padding: 8px 14px;
  border-radius: 8px;
  outline: none;
  width: 240px;
  transition: border-color 0.2s;
}
.search-input::placeholder { color: var(--text-dim); }
.search-input:focus { border-color: var(--accent); }

.groups-grid {
  display: grid;
  /* grid-template-columns: repeat(auto-fill, minmax(280px, 1fr)); */
  grid-template-columns: repeat(2, 1fr);
  gap: 14px;
}

.card {
  background: var(--bg-surface);
  border: 1px solid var(--border);
  border-radius: 14px;
  padding: 16px 18px;
}

.section-eyebrow {
  display: block;
  font-size: 10px;
  font-weight: 600;
  letter-spacing: 0.12em;
  text-transform: uppercase;
  color: var(--accent);
  font-family: 'JetBrains Mono', monospace;
  margin-bottom: 3px;
}

.card-title {
  font-size: 14px;
  font-weight: 600;
  color: var(--text-primary);
  margin: 0 0 12px;
}

.entry-list {
  list-style: none;
  margin: 0;
  padding: 0;
  display: flex;
  flex-direction: column;
  gap: 1px;
}

.entry-row {
  display: flex;
  justify-content: space-between;
  align-items: baseline;
  gap: 8px;
  padding: 4px 6px;
  border-radius: 6px;
  transition: background 0.15s;
}

.entry-row:hover {
  background: var(--bg-raised);
}

.entry-label {
  font-size: 12px;
  color: var(--text-muted);
  flex: 1;
  min-width: 0;
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
}

.entry-numbers {
  display: flex;
  gap: 8px;
  align-items: center;
  flex-shrink: 0;
}

.entry-ext {
  font-size: 11px;
  color: var(--text-dim);
  font-family: 'JetBrains Mono', monospace;
}

.entry-number {
  font-size: 12px;
  font-weight: 500;
  color: var(--accent);
  font-family: 'JetBrains Mono', monospace;
  white-space: nowrap;
}
</style>
