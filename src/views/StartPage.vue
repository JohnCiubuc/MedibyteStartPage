<template>
  <div class="page">
    <div class="scanlines" aria-hidden="true" />
    <main class="main">
<WetReadTitle :currentMode="activeMode" @modeChange="activeMode = $event" />
<!-- then conditionally render your views: -->
<template v-if="activeMode === 'quickaccess'">

  <Transition name="fade-slide" appear>
    <SearchBar @queryChanged="searchChange" />
  </Transition>
  <Transition name="fade-slide" appear>
    <QuickAccess :tiles="soloTiles" />
  </Transition>
  <Transition name="fade-slide" appear>
    <TileGroup eyebrow="QGenda Medicine Teams"
      title="Make sure you're logged into QGenda first!" :tiles="qgendaMedicineTiles"
      :filter="searchQuery" />
  </Transition>
  <Transition name="fade-slide" appear>
    <TileGroup eyebrow="Anatomical Reference Library"
      title="eAnatomy - Your anatomical atlas for everything anatomical" :tiles="eAnatomyTiles"
      :filter="searchQuery" />
  </Transition>
  <Transition name="fade-slide" appear>
    <TileGroup eyebrow="Rad-Call Quick Links" title="Rad-Call - Need recommendations or measurements?"
      :tiles="radCallTiles" :filter="searchQuery" />
  </Transition>
  <Transition name="fade-slide" appear>
    <MSKMriTileGroup eyebrow="The MSK MRI Atlas by Alex Freitas, MD" title="View integrated MRIs right here"
      :tiles="MSKTiles" :filter="searchQuery" />
  </Transition>
  <Transition name="fade-slide" appear>
    <TileGroup eyebrow="Rad Assistant Quick Links" title="Rad Assistant" :tiles="radAssistantTiles"
      :filter="searchQuery" />
  </Transition>
  <Transition name="fade-slide" appear>
    <TileGroup eyebrow="The wettest journal articles"
      title="Yeah, I know you can't change this list but trust me, they'll come in handy" :tiles="articleTiles" />
  </Transition>
  </template>

<template v-else-if="activeMode === 'followup'">
  <Transition name="fade-slide" appear>
    <StudyFollowup/>
  </Transition>
</template>

<template v-else-if="activeMode === 'phone'">
  <Transition name="fade-slide" appear>
    <PhoneDirectory/>
  </Transition>
</template>

<template v-else-if="activeMode === 'caselog'">
  <Transition name="fade-slide" appear>
    <CaseLog/>
  </Transition>
</template>

<template v-else-if="activeMode === 'responsible'">
  <Transition name="fade-slide" appear>
    <ResponsibilitiesOverview/>
  </Transition>
</template>


      <p class="feedback">
        Any questions, suggestions, or improvements?
        <a href="mailto:john.ciubuc@gmail.com" class="feedback-link">Email me</a>
      </p>
<CounterAPI />
      <p class="version">
        <button class="learn-more" @click="showVersion = !showVersion">TheWetRead v1.1.2</button>
      </p>
      <div v-if="showVersion" class="help-panel">

        <p class="help-title">v1.1.2</p>
        <ul class="version-list">
          <li>Added QGenda Medicine Team Links</li>
          <li>Re-skinned everything to ugly turquoise black</li>
        </ul>
        <p class="help-title">v1.1.1</p>
        <ul class="version-list">
          <li>Added resident phone number directory</li>
          <li>Added staff/resident phone number directory</li>
          <li>Added staff/resident phone number quick search with 'c'</li>
        </ul>
        <p class="help-title">v1.1.0</p>
        <ul class="version-list">
          <li>Responsibilities dim per current active time</li>
        </ul>
        <p class="help-title">v1.0.9</p>
        <ul class="version-list">
          <li>Added Responsibilities</li>
        </ul>
        <p class="help-title">v1.0.8</p>
        <ul class="version-list">
          <li>Added PhoneDirectory</li>
        </ul>
        <p class="help-title">v1.0.7</p>
        <ul class="version-list">
          <li>Removed Community Caselog Module</li>
        </ul>
        <p class="help-title">v1.0.6</p>
        <ul class="version-list">
          <li>Community Caselog Module</li>
        </ul>
        <p class="help-title">v1.0.5</p>
        <ul class="version-list">
          <li>Added animations to The Wet Read title</li>
          <li>Transition pages and animations between Quick Access and Study Followup</li>
          <li>Study Followup module</li>
        </ul>
        <p class="help-title">v1.0.4</p>
        <ul class="version-list">
          <li>Added Pediatric Bone-XRay Quick Access</li>
          <li>Added Diagnostic Utility of CT and Fluoroscopic Esophagography</li>
          <li>Added How to write a good report</li>
          <li>Added email link</li>
        </ul>
        <p class="help-title">v1.0.3</p>
        <ul class="version-list">
          <li>The Wet Read</li>
          <li>Mobile-friendly interface</li>
          <li>Unified search - also searches the modules below as you type</li>
          <li>Moved Rad Assistant below the MSK Atlas</li>
          <li>Added QGenda, RADPrimer, CaseStacks, ACGME Case Logs, AnkiWeb, and Radiographics quick access</li>
          <li>Removed Rad-Call and 9Gag quick access</li>
          <li>Added Rad-Call quick links</li>
          <li>New Innovations quick access added</li>
        </ul>
        <p class="help-title">v1.0.2</p>
        <ul class="version-list">
          <li>Integrated the MSK MRI Atlas by Alex Freitas, MD</li>
          <li>eAnatomy Loose Bang module search</li>
          <li>Populated additional Rad Assistant tiles</li>
          <li>Added UHSTX Remote Access Tile</li>
          <li>Removed MSK MRI Atlas tile</li>
        </ul>
        <p class="help-title">v1.0.1</p>
        <ul class="version-list">
          <li>Links open in-page rather than in a new tab</li>
          <li>Fixed spacing and size of tiles</li>
          <li>Increased TileGroups to show 8 tiles in a row (from 5)</li>
          <li>Added new Loose Bangs: OpenEvidence, PubMed, Google Scholar</li>
          <li>Added CT Cirrhotic Abdomen, CTA Lower Extremity, CT Ankle and Foot</li>
        </ul>
      </div>
    </main>
    <AppFooter />
  </div>
</template>

<script setup>
import { ref } from 'vue'
import SearchBar from '@/components/SearchBar.vue'
import WetReadTitle from '@/components/WetReadTitle.vue'
import TileGroup from '@/components/TileGroup.vue'
import QuickAccess from '@/components/QuickAccess.vue'
import AppFooter from '@/components/AppFooter.vue'
import MSKMriTileGroup from '@/components/MSKMriTileGroup.vue'
import CounterAPI from '@/components/CounterAPI.vue'
import StudyFollowup from '@/components/StudyFollowup.vue'
// import CaseLog from '@/components/CaseLog.vue'
import PhoneDirectory from '@/components/PhoneDirectory.vue'
import ResponsibilitiesOverview from '@/components/ResponsibilitiesOverview.vue'

const showVersion = ref(false)
const searchQuery = ref('')
const activeMode = ref('')
activeMode.value = "quickaccess";

const articleTiles = [
  { label: 'CT Esophagography is Better than Fluoro', icon: 'https://thewetread.com/ajr.png', url: 'https://ajronline.org/doi/10.2214/AJR.19.22166', keywords: 'barium esophgagram ct no' },
  { label: 'Stop Writing Bad Reports', icon: 'https://external-content.duckduckgo.com/ip3/www.rsna.org.ico', url: 'https://pubs.rsna.org/doi/10.1148/rg.2020200020', keywords: 'write a good report' },
    ]

const eAnatomyTiles = [
  { label: 'MRI Brain', icon: 'https://cdn1.imaios.com/i/images/4/2/6/0/310624-3-eng-GB/f9296ee2c558-brain3dmri.png?q=75&w=230&s=6419caa48600b24d59e4ff706f7222d3', url: 'https://www.imaios.com/en/e-anatomy/brain/mri-brain', keywords: 'neuro neurology neuroanatomy brain stroke head skull cranial' },
  { label: 'MRA Brain', icon: 'https://cdn1.imaios.com/i/images/7/0/5/0/310507-3-eng-GB/8e9d6b5348c0-cerveau.png?q=75&w=230&s=894b4aa98935c59886c5fceb2c1b00a6', url: 'https://www.imaios.com/en/e-anatomy/brain/mri-axial-brain', keywords: 'mra angio stroke brain neuro neurology neuroanatomy head skull cranial' },
  { label: 'MR Venography', icon: 'https://cdn1.imaios.com/i/images/2/0/0/3/9623002-1-eng-GB/brain-venography-mr.png?q=75&w=230&s=7df186bb8eb87ff1fb5d0acf4044bbb5', url: 'https://www.imaios.com/en/e-anatomy/brain/mr-cerebral-venography', keywords: 'mrv venography veno angio stroke brain neuro neurology neuroanatomy head skull cranial' },
  { label: 'CT Brain', icon: 'https://cdn1.imaios.com/i/images/5/8/5/0/310585-3-eng-GB/0a8f1dd62341-brain-ct.png?q=75&w=230&s=e2205d9962bc65dab16ad83e39523668', url: 'https://www.imaios.com/en/e-anatomy/brain/ct-brain', keywords: 'neuro neurology neuroanatomy head skull cranial' },
  { label: 'CT Head and Neck', icon: 'https://cdn1.imaios.com/i/images/2/9/7/1/131792-4-eng-GB/caebb438ec94-Head-Neck-CT.png?q=75&w=230&s=ff1fb7b910c5e632425be1b464c46e63', url: 'https://www.imaios.com/en/e-anatomy/head-and-neck/ct-head-and-neck', keywords: 'neck throat larynx pharynx thyroid' },
  { label: 'CT Face', icon: 'https://cdn1.imaios.com/i/images/6/8/7/1/131786-5-eng-GB/e4a720e3e88b-face.png?q=75&w=230&s=abfc4f7f9c056c02ed7ebf8e8f5863ee', url: 'https://www.imaios.com/en/e-anatomy/head-and-neck/ct-face', keywords: 'facial maxillofacial sinus orbit' },
  { label: 'CT Temporal Bone', icon: 'https://cdn1.imaios.com/i/images/9/8/8/0/310889-3-eng-GB/20f6136a1276-rocher.png?q=75&w=230&s=b81e6b55fef2c059926768851a4e7555', url: 'https://www.imaios.com/en/e-anatomy/head-and-neck/ct-temporal-bone', keywords: 'ear inner ear mastoid petrous rocher otology' },
  { label: 'MRI Cervical Spine', icon: 'https://cdn1.imaios.com/i/images/5/3/7/1/131735-3-eng-GB/ce0f1ce03657-cervical-spine-mri.png?q=75&w=230&s=1fa324b450d74a2b19a517f45ab2f5d3', url: 'https://www.imaios.com/en/e-anatomy/spine/mri-cervical-spine', keywords: 'neck spine c-spine vertebrae' },
  { label: 'MRI Lumbar Spine', icon: 'https://cdn1.imaios.com/i/images/7/8/0/0/460087-2-eng-GB/95c684afdf4e-lumbar-spine-mri.png?q=75&w=230&s=ff4c05c22f7cf136b37185de1a7db329', url: 'https://www.imaios.com/en/e-anatomy/spine/mri-lumbar-spine', keywords: 'back spine l-spine vertebrae disc' },
  { label: 'CT Lumbar Spine', icon: 'https://cdn1.imaios.com/i/images/8/3/7/1/131738-3-eng-GB/080c9bb3cb09-lumbar-spine-ct.png?q=75&w=230&s=51f61d0d7765ccb3c5bd461d51974a8c', url: 'https://www.imaios.com/en/e-anatomy/spine/ct-lumbar-spine', keywords: 'back spine l-spine vertebrae disc' },
  { label: 'CT Lymph Nodes', icon: 'https://cdn1.imaios.com/i/images/3/5/4/0/310453-3-eng-GB/9b48c06243a6-aires-radiotherapie.png?q=75&w=230&s=1f7bcac75a4a0a7563ace0fa47a11c56', url: 'https://www.imaios.com/en/e-anatomy/whole-body/ct-body-lymph-nodes', keywords: 'lymphatic nodal stations oncology radiotherapy body' },
  { label: 'CT Chest', icon: 'https://cdn1.imaios.com/i/images/9/5/1/1/311159-5-eng-GB/686203b08da4-thorax.png?q=75&w=230&s=3238dc3c9b34c0477182df07e6389490', url: 'https://www.imaios.com/en/e-anatomy/thorax/ct-axial-chest', keywords: 'thorax lung lungs pulmonary thoracic' },
  { label: 'MRCP', icon: 'https://cdn1.imaios.com/i/images/5/1/3/1/311315-4-eng-GB/220cb5a0c48b-cholangiopancreatography-mr.png?q=75&w=230&s=72b7f3a4822055d81645dcc72e79dcc7', url: 'https://www.imaios.com/en/e-anatomy/abdomen-and-pelvis/magnetic-resonance-cholangiopancreatography', keywords: 'biliary pancreas pancreatic gallbladder bile duct hepatobiliary body' },
  { label: 'CT Abdomen and Pelvis', icon: 'https://cdn1.imaios.com/i/images/3/9/3/1/311393-4-eng-GB/1cff6e56270a-abdomen.png?q=75&w=230&s=3d7cf3818d92511c44812c86b0d7a3b7', url: 'https://www.imaios.com/en/e-anatomy/abdomen-and-pelvis/ct-axial-male-abdomen-and-pelvis', keywords: 'abdomen pelvis abdominal gi gastrointestinal body' },
  { label: 'CT Cirrhotic Abdomen and Pelvis', icon: 'https://medibyte.app/sick_abd.jpg', url: 'https://www.imaios.com/en/e-anatomy/abdomen-and-pelvis/ct-peritoneal-cavity', keywords: 'cirrhosis liver peritoneal peritoneum ascites body' },
  { label: 'Radiograph Upper Extremity', icon: 'https://cdn1.imaios.com/i/images/0/6/5/0/310560-4-eng-GB/afc5ddc0b3b9-ms-radios.png?q=75&w=230&s=3f30892607555d5a4a0382f0e512e5c3', url: 'https://www.imaios.com/en/e-anatomy/upper-limb/radiography-upper-extremity', keywords: 'arm hand wrist elbow shoulder xray x-ray upper limb' },
  { label: 'Radiograph Lower Extremity', icon: 'https://cdn1.imaios.com/i/images/1/1/7/1/131711-4-eng-GB/04ec3cbb1fae-membre-inferieur-radiographies.png?q=75&w=230&s=34083eee34a73622cf8903ed283c1500', url: 'https://www.imaios.com/en/e-anatomy/lower-limb/radiography-lower-extremity', keywords: 'leg knee hip xray x-ray lower limb' },
  { label: 'CTA Lower Extremity', icon: 'https://cdn1.imaios.com/i/images/2/3/8/3/12723832-2-eng-GB/5f2c8a3e5e4f-lower-limb-cta.png?q=75&w=230&s=243825fed41448e7971262f9a49b23cf', url: 'https://www.imaios.com/en/e-anatomy/lower-limb/lower-limb-cta', keywords: 'angiogram angiography vascular runoff leg arteries' },
  { label: 'CT Ankle and Foot', icon: 'https://cdn1.imaios.com/i/images/2/1/1/5/14435112-1-eng-GB/ca1bfee21c6b-ankle-foot-ct.png?q=75&w=230&s=6277c70aed8d1ca8dcde08c65c5d1351', url: 'https://www.imaios.com/en/e-anatomy/lower-limb/ankle-and-foot-ct', keywords: 'ankle foot podiatry' },
]

const radCallTiles = [
  { label: 'Neuroradiology', icon: 'https://thewetread.com/tile-neuro.png', url: 'https://rad-call.com/app.html#neuro', keywords: 'neuro brain spine head stroke aspects mca perfusion cervical spine ligamentous trauma thoracolumbar ao rigid hematoma mandib maxillofacial neck temporal orbit sinusitis odontogenic mouth tooth peritonsillar tonsil retropharyngeal abscess epiglottitis salivary gland otitis mastoiditis lemierre angioedema infection cord dural sinus intracranial pituitary PRES shunt lumbar disc' },
  { label: 'Chest', icon: 'https://thewetread.com/tile-chest.png', url: 'https://rad-call.com/app.html#chest', keywords: 'thorax lung pulmonary thoracic aorta aortic cardiac pericard diaphragm pneumonia infection pleural dissection pe embolism ild uip nsip fleischner ggo ground glass opacity edema ards aspiration' },
  { label: 'Abdomen', icon: 'https://thewetread.com/tile-body.png', url: 'https://rad-call.com/app.html#body', keywords: 'abdominal pelvis gi gastrointestinal body aast liver renal kidney spleen splenic bladder rupture pancreas bowel hollow viscus pregnancy gunshot appendicitis adrenal washout ischemia obstruction gallbladder biliary diverticulitis ectopic epiploic omental infarct hernia ovarian torsion pancreatitis volvulus' },
  { label: 'MSK', icon: 'https://thewetread.com/tile-msk.png', url: 'https://rad-call.com/app.html#msk', keywords: 'musculoskeletal bone joint orthopedic ortho acetabular ankle shoulder clavicle compartment humerus forearm arm elbow foot hand wrist knee tibia fibula pelvic femur humerus scapula nec fasc osteomyelitis arthritis fracture' },
  { label: 'Ultrasound', icon: 'https://thewetread.com/tile-us.png', url: 'https://rad-call.com/app.html#us', keywords: 'sonography sono us doppler carotid ica stenosis adnexal cyst ovarian hydronephrosis kidney renal liver ruq transplant dvt ob retained conception retroperitoneal transplant testicular scrotal torsion tips dips' },
  { label: 'Pediatrics', icon: 'https://thewetread.com/tile-peds.png', url: 'https://rad-call.com/app.html#peds', keywords: 'peds pediatric children kids lines tubes ivh intraventricular hemorrhage pyloric stenosis intussusception malrotation volvulus developmental dysplasia hip scfe slipped capital femoral epiphysis hydronephrosis kidney renal salter harris elbow critoe forearm arm fracture nat non accidental trauma' },
  { label: 'Contrast Rxn / PreMed', icon: 'https://www.acr.org/_next/image?url=https%3A%2F%2Fedge.sitecorecloud.io%2Famericancoldf5f-acrorgf92a-productioncb02-3650%2Fmedia%2FACR%2FImages%2FLogos%2F1x1%2FContrast-Manual.jpg%3Fh%3D1200%26iar%3D0%26w%3D1200&w=3200&q=75', url: 'https://rad-call.com/app.html#contrast', keywords: 'allergy allergic reaction premedication anaphylaxis iodinated contrast acr' },
  { label: 'MRI Safety', icon: 'https://www.mrisafety.com/images/BraccoLogo100.png', url: 'https://www.mrisafety.com/TMDL_list.php?orderby=alist_description', keywords: 'implant pacemaker device compatibility screening mri safety' },
]


const qgendaMedicineTiles = [
  { label: 'Medicine A', icon: 'https://thewetread.com/A.jpg', url: 'https://app.qgenda.com/link/view?linkKey=ddbafa6a-7fbb-4eec-b4bf-b3a3a6049e2d', keywords: '' },
  { label: 'Medicine B', icon: 'https://thewetread.com/B.jpg', url: 'https://app.qgenda.com/link/view?linkKey=7334ab72-926d-417c-9abe-bc44dedd3daa', keywords: '' },
  { label: 'Medicine C', icon: 'https://thewetread.com/C.jpg', url: 'https://app.qgenda.com/link/view?linkKey=cf57d22f-c023-4262-90a8-374743e17a6b', keywords: '' },
  { label: 'Medicine D', icon: 'https://thewetread.com/D.jpg', url: 'https://app.qgenda.com/link/view?linkKey=4d82fd4e-f6e2-4073-b4b8-ba5d6a8ac2d6', keywords: '' },
  { label: 'Medicine E', icon: 'https://thewetread.com/E.jpg', url: 'https://app.qgenda.com/link/view?linkKey=9cc046f5-6216-4af3-90dc-de7d2cbe046d', keywords: '' },
  { label: 'Medicine F', icon: 'https://thewetread.com/F.jpg', url: 'https://app.qgenda.com/link/view?linkKey=34d04b08-6c1c-4a8d-9f3b-2fd393ddede6', keywords: '' },
  { label: 'Medicine G', icon: 'https://thewetread.com/G.jpg', url: 'https://app.qgenda.com/link/view?linkKey=f688b33c-e255-4396-b32b-9f71aaff7af4', keywords: '' },
  { label: 'Medicine H', icon: 'https://thewetread.com/H.jpg', url: 'https://app.qgenda.com/link/view?linkKey=c7a9e9d2-4a3b-493e-b6ce-698edb36c4cd', keywords: '' },
  { label: 'Medicine J', icon: 'https://thewetread.com/J.jpg', url: 'https://app.qgenda.com/link/view?linkKey=30522dc3-2940-4310-8caa-a52ba806feaa', keywords: '' },
  { label: 'Medicine K-N', icon: 'https://thewetread.com/KN.jpg', url: 'https://app.qgenda.com/link/view?linkKey=b775a6ee-8ad1-4e19-8f7f-1b4fbae000ec', keywords: '' },
  { label: 'Medicine 1', icon: 'https://thewetread.com/1.jpg', url: 'https://app.qgenda.com/link/view?linkKey=436de694-dbc8-4791-8288-7a0326f643a7', keywords: '' },
  { label: 'Medicine 2', icon: 'https://thewetread.com/2.jpg', url: 'https://app.qgenda.com/link/view?linkKey=8d22f038-820d-499e-9a96-2e6a55c38c41', keywords: '' },
  { label: 'Medicine 3', icon: 'https://thewetread.com/3.jpg', url: 'https://app.qgenda.com/link/view?linkKey=a5545187-3dcb-4113-832c-fae8566d93a1', keywords: '' },
  { label: 'Medicine 4', icon: 'https://thewetread.com/4.jpg', url: 'https://app.qgenda.com/link/view?linkKey=b5345e25-5cf2-4bb2-b4f6-96591b0fffb3', keywords: '' },
  { label: 'Medicine 5', icon: 'https://thewetread.com/5.jpg', url: 'https://app.qgenda.com/link/view?linkKey=38a53100-6118-4c75-8366-d23bc4b15aaa', keywords: '' },
  { label: 'Medicine 6', icon: 'https://thewetread.com/6.jpg', url: 'https://app.qgenda.com/link/view?linkKey=d1920eb2-6968-4224-ba20-206053e5556a', keywords: '' },
  { label: 'Trauma / Pit Boss', icon: 'https://thewetread.com/trauma.jpg', url: 'https://app.qgenda.com/link/view?linkKey=0fd94536-a3a2-42be-983a-33c581ef676d', keywords: '' },
]

const radAssistantTiles = [
  { label: 'Peds Elbow Fractures', icon: 'https://radiologyassistant.nl/assets/elbow-fractures-in-children/a50979768b5d3a_critoe2.jpg', url: 'https://radiologyassistant.nl/pediatrics/hip/fractures-in-children-1', keywords: 'pediatric elbow fracture children critoe ossification' },
  { label: 'TI-RADS', icon: 'https://radiologyassistant.nl/assets/_1a-tab-tirads-landscape.jpg', url: 'https://radiologyassistant.nl/head-neck/ti-rads/ti-rads', keywords: 'thyroid nodule ultrasound classification' },
  { label: 'Thoracolumbar Fractures', icon: 'https://radiologyassistant.nl/assets/1-tek-intro-c-1709283624.jpg', url: 'https://radiologyassistant.nl/musculoskeletal/spine/ao-classification', keywords: 'spine fracture ao classification trauma vertebral' },
  { label: 'Bowel Wall Thickening', icon: 'https://radiologyassistant.nl/assets/bowel-wall-thickening-ct-pattern/a5343fb7609022_7-normal-enhancement.jpg', url: 'https://radiologyassistant.nl/abdomen/bowel/bowel-wall-thickening-ct-pattern', keywords: 'bowel colitis enteritis ct pattern intestine' },
  { label: 'Child Abuse Trauma', icon: 'https://radiologyassistant.nl/assets/diagnostic-imaging-in-child-abuse/a50979771710d1_corner.jpg', url: 'https://radiologyassistant.nl/pediatrics/child-abuse/diagnostic-imaging-in-child-abuse', keywords: 'pediatric nonaccidental trauma nat fracture skeletal survey' },
  { label: 'Adrenal Lesions', icon: 'https://radiologyassistant.nl/assets/adrenals-lesion-characterization/a5c9619ed4a566_____Pheo-1.jpg', url: 'https://radiologyassistant.nl/abdomen/adrenals/lesion-characterization-1', keywords: 'adrenal adenoma pheochromocytoma incidentaloma' },
  { label: 'Gallbladder Obstruction', icon: 'https://radiologyassistant.nl/assets/4-tab-gallstone-complication-1574505520.png', url: 'https://radiologyassistant.nl/abdomen/biliary-system/lk-jg', keywords: 'gallstone cholecystitis biliary obstruction cholelithiasis' },
  { label: 'Bile Duct Pathology', icon: 'https://radiologyassistant.nl/assets/biliary-ducts-pathology/a50979796b82f9_Afbeelding-8.jpg', url: 'https://radiologyassistant.nl/abdomen/biliary-system/biliary-duct-pathology', keywords: 'biliary cbd choledocholithiasis cholangitis' },
  { label: 'Gallbladder Thickening', icon: 'https://radiologyassistant.nl/assets/gallbladder-wall-thickening/a509797711ac0b_Fig-2.jpg', url: 'https://radiologyassistant.nl/abdomen/biliary-system/gallbladder-wall-thickening', keywords: 'cholecystitis gallbladder wall biliary' },
  { label: 'Ovarian Cysts', icon: 'https://radiologyassistant.nl/assets/ovarian-cysts-common-lesions/a509797a73751a_TEK-ovulation.jpg', url: 'https://radiologyassistant.nl/abdomen/unsorted/common-ovarian-cystic-lesions', keywords: 'ovary gynecologic adnexal pelvis' },
  { label: 'LI-RADS', icon: 'https://radiologyassistant.nl/assets/tab-3f-features.jpg', url: 'https://radiologyassistant.nl/abdomen/liver/li-rads', keywords: 'liver hcc hepatocellular carcinoma cirrhosis classification' },
  { label: 'Acute Pancreatitis', icon: 'https://radiologyassistant.nl/assets/pancreas-acute-pancreatitis-2-0/a5542781188628_1TAB.png', url: 'https://radiologyassistant.nl/abdomen/pancreas/acute-pancreatitis', keywords: 'pancreas pancreatic inflammation' },
]

const soloTiles = [
  // { label: 'Rad-Call', icon: 'https://external-content.duckduckgo.com/ip3/rad-call.com.ico', url: 'https://rad-call.com/app.html' },
  // { label: '9GAG', icon: 'https://cdn-icons-png.flaticon.com/512/2111/2111316.png', url: 'https://9gag.com' },
  { label: 'Outlook', icon: 'https://thewetread.com/outlook.jpg', url: 'https://outlook.office365.com/mail/' },
  { label: 'QGenda', icon: 'https://thewetread.com/qi.jpg', url: 'https://app.qgenda.com/Dashboard/Company/2b10db48-c04e-4028-86ca-940a03bd9dd0' },
  { label: 'New Innovation', icon: 'https://thewetread.com/ni.jpg', url: 'https://www.new-innov.com/Login/Home.aspx' },

  { label: 'RADPrimer', icon: 'https://thewetread.com/radprimer.jpg', url: 'https://app.radprimer.com/curriculum' },
  { label: 'CaseStacks', icon: 'https://thewetread.com/casestacks.jpg', url: 'https://casestacks.com/dashboard' },
  { label: 'AnkiWeb', icon: 'https://thewetread.com/anki.jpg', url: 'https://ankiweb.net/decks' },
  { label: 'Pedi Bone-XRay', icon: 'https://thewetread.com/bone.jpg', url: 'https://bonexray.com/' },
  { label: 'OpenEvidence', icon: 'https://thewetread.com/OE.jpg', url: 'https://www.openevidence.com/' },
  // { label: 'Qualified Health', icon: 'https://www.finsmes.com/wp-content/uploads/2026/03/Qualified-Health.jpeg', url: 'https://chat.qualifiedhealthai.com/' },
  { label: 'UHS Anywhere', icon: 'https://thewetread.com/uhs.jpg', url: 'https://anywhere.uhstx.com/logon/LogonPoint/index.html' },
  { label: 'Radiographics', icon: 'https://thewetread.com/R.jpg', url: 'https://pubs.rsna.org/journal/radiographics' },
  { label: 'ACGME Cases', icon: 'https://thewetread.com/acgme.jpg', url: 'https://apps.acgme.org/ads/CaseLogs/CaseEntry/Insert' },
  { label: 'Resident Direct', icon: 'https://thewetread.com/resident.jpg', url: 'https://login.mdstaff.com/uhssa/epriv?AuthCode=A347E2B4-BF88-40FE-A18A-FB8DBF112D91' },
  // { label: 'Contact Staff', icon: 'https://thewetread.com/attending.png', url: 'https://app.qgenda.com/OnCallSearch/68e7ce6d-aae2-453b-bc2e-01918097b1f5?isFromLandingPage=true' },

// https://app.radprimer.com/curriculum
// 


]
const MSKTiles = [
  { label: 'Knee',     icon: 'https://freitasrad.net/images/MR_4.jpg',     maxNumber: 83, keywords: 'knee meniscus acl mcl patella msk' },
  { label: 'Shoulder', icon: 'https://freitasrad.net/images/shoulder_sag2.jpg', maxNumber: 79, keywords: 'shoulder rotator cuff labrum glenoid msk' },
  { label: 'Shoulder_Arthro', icon: 'https://freitasrad.net/pages/atlas/Shoulder_Arthro/29.jpg', maxNumber: 46, keywords: 'shoulder arthrogram labrum mr arthrography msk' },
  { label: 'Ankle',    icon: 'https://freitasrad.net/images/anarrs.jpg',    maxNumber: 47, keywords: 'ankle ligament foot mortise msk' },
  { label: 'Elbow',    icon: 'https://freitasrad.net/images/elbow2.jpg',    maxNumber: 62, keywords: 'elbow ucl epicondyle msk' },
  { label: 'Wrist',    icon: 'https://freitasrad.net/images/wrist_cor_T1.jpg',    maxNumber: 45, keywords: 'wrist hand carpal tfcc scaphoid msk' },
  { label: 'Hip',      icon: 'https://freitasrad.net/images/hip3.jpg',      maxNumber: 97, keywords: 'hip labrum acetabulum femoral msk' },
]

function searchChange(query) {
  searchQuery.value = query;
}


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
  font-size: 32px;
  font-weight: 600;
  letter-spacing: -0.01em;
  color: var(--text-primary);
  line-height: 1;
}
.title_small {
  font-size: 10px;
  font-weight: 200;
  letter-spacing: -0.01em;
  color: var(--text-primary);
  font-style: oblique;
  line-height: 1;
  margin-top: -10px;
}

.version {
  margin: 7px;
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
  margin-top: 10px;
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
  content: '-';
  color: var(--accent);
  margin-right: 8px;
}
.feedback {
  font-size: 14px;
  font-family: 'JetBrains Mono', monospace;
  color: var(--text-muted);
  text-align: center;
}
.feedback-link {
  color: var(--accent);
  text-decoration: underline;
  text-underline-offset: 2px;
}
.feedback-link:hover { opacity: 0.75; }
/* in your global styles or the parent component */
.fade-slide-enter-active,
.fade-slide-leave-active {
  transition: opacity 0.3s ease, transform 0.3s ease;
}
.fade-slide-enter-from,
.fade-slide-leave-to {
  opacity: 0;
  transform: translateY(8px);
}
</style>
