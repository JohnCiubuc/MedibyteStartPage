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
    eyebrow: 'UT 6th Floor',
    title: 'R-2 (8 readers)',
    entries: [
      { label: 'Edie', number: '567-5552' },
      { label: 'Mary Lou', number: '450-9738' },
      { label: 'Lissette', number: '567-6482' },
      { label: 'Fulton', extCell: '203-4731', number: '(505)670-7607' },
      { label: 'Jackson', extCell: '203-9371', number: '(979)218-6135' },
      { label: 'Lu', extCell: '203-4040', number: '(214)499-7097' },
      { label: 'Singh', extCell: '203-9159', number: '(832)725-8512' },
      { label: 'Stephens', extCell: '203-3100', number: '(208)540-2072' },
      { label: 'Tiwari', extCell: '203-5061', number: '(830)469-7364' },
      { label: 'Yeganeh', extCell: '203-9003', number: '(858)405-0281' },
      { label: 'Zhang', extCell: '203-3856', number: '(903)327-2580' },
    ],
  },
  {
    eyebrow: 'UT 6th Floor',
    title: 'R-4 (10 readers)',
    entries: [
      { label: 'PRodriguez', extCell: '203-2362', number: '618-2015' },
      { label: 'Alizai', extCell: '203-9408', number: '(347)701-6919' },
      { label: 'Kuo', extCell: '203-1309', number: '(404)822-9573' },
      { label: 'Chiang', extCell: '203-3027', number: '(303)990-1768' },
      { label: 'Amanullah', extCell: '203-2661', number: '246-3825' },
      { label: 'Darling', extCell: '203-6341', number: '845-6845' },
      { label: 'Haq', extCell: '203-7128', number: '(281)935-7470' },
      { label: 'Kapalczynski', extCell: '203-5446', number: '(502)797-4422' },
      { label: 'Khalil', extCell: '203-3907', number: '(281)705-4678' },
      { label: 'Nguyen', extCell: '203-8432', number: '(206)372-4342' },
      { label: 'Sandhu', extCell: '203-2783', number: '(832)962-6526' },
      { label: 'Sura', extCell: '203-3890', number: '(979)492-7474' },
      { label: 'Wang', extCell: '203-9448', number: '(956)792-0008' },
      { label: 'Walters', extCell: '203-0571', number: '(214)532-7086' },
    ],
  },
  {
    eyebrow: 'UT 6th Floor',
    title: 'R-1 (9 readers)',
    entries: [
      { label: 'Daftaribesheli', extCell: '203-3264', number: '(617)755-8772' },
      { label: 'Gregorat', extCell: '203-8794', number: '(214)597-2679' },
      { label: 'Vela', extCell: '203-7009', number: '(956)231-4748' },
      { label: 'Mandal', extCell: '203-3037', number: '(409)651-3476' },
      { label: 'Ortega', extCell: '203-0860', number: '(210)216-6538' },
      { label: 'Rajebi', extCell: '203-8860', number: '(315)480-0104' },
      { label: 'ERodriguez', extCell: '203-2664', number: '(831)359-6405' },
      { label: 'Kaur', extCell: '203-0884', number: '(210)840-2035' },
      { label: 'Venkataraman', extCell: '203-9659', number: '(408)442-9696' },
    ],
  },
  {
    eyebrow: 'UT 6th Floor — Fellows',
    title: 'Fellows',
    entries: [
      { label: 'Zahid (Chest)', extCell: '203-1921', number: '(520)891-3364' },
      { label: 'Chettri (Body)', extCell: '203-1896', number: '(612)900-8718' },
      { label: 'Miller (Body)', extCell: '203-2175', number: '(804)822-8201' },
      { label: 'Zafar (Body)', extCell: '203-9117', number: '(401)369-3159' },
      { label: 'Ramsey (IR)', extCell: '203-4980', number: '(646)701-4582' },
      { label: 'Tahir (IR)', extCell: '203-3626', number: '(610)703-4817' },
      { label: 'Griffith (MSK)', extCell: '203-1638', number: '(719)963-5660' },
      { label: 'Lopez (Neuro)', extCell: '203-9301', number: '(787)638-9272' },
      { label: 'Mudasiru (Mammo)', extCell: '203-3271', number: '(615)364-1181' },
      { label: 'Kadaba (Mammo)', extCell: '', number: '' },
      { label: 'Krause', extCell: '203-3695', number: '(706)296-6194' },
      { label: 'Koenig', extCell: '203-1603', number: '557-6861' },
      { label: 'Nanyes', extCell: '203-3433', number: '(830)968-0240' },
      { label: 'Pluguez', extCell: '203-9781', number: '(787)688-7601' },
      { label: 'Pong', extCell: '203-7035', number: '(281)451-4199' },
      { label: 'Vargas', extCell: '203-2341', number: '(830)734-1511' },
      { label: 'Wilcoxson', extCell: '203-9688', number: '(801)698-9246' },
      { label: 'Estrada', extCell: '203-5816', number: '885-1598' },
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
  grid-template-columns: repeat(3, 1fr);
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
