<template>
  <div class="page">
    <div class="scanlines" aria-hidden="true" />
    <main class="main">
      <WetReadTitle/>
      <!-- <h1 class="title">The Wet Read</h1> -->
      <!-- <h2 class="title_small">So wet that you can feel it...</h2> -->
      <SearchBar @queryChanged="searchChange"  />
      <QuickAccess :tiles="soloTiles" />
      <TileGroup eyebrow="Anatomical Reference Library" title="eAnatomy - Your anatomical atlas for everything anatomical" :tiles="eAnatomyTiles" :filter="searchQuery"/>
      <TileGroup eyebrow="Rad-Call Quick Links" title="Rad-Call - Need recommendations or measurements?" :tiles="radCallTiles" :filter="searchQuery"/>
      <MSKMriTileGroup eyebrow="The MSK MRI Atlas by Alex Freitas, MD" title="View integrated MRIs right here" :tiles="MSKTiles" :filter="searchQuery"/>
      <TileGroup eyebrow="Rad Assistant Quick Links" title="Rad Assistant" :tiles="radAssistantTiles"  :filter="searchQuery"/>
      <p class="version">
        <button class="learn-more" @click="showVersion = !showVersion">TheWetRead v1.0.3</button>
      </p>
      <div v-if="showVersion" class="help-panel">
        <p class="help-title">v1.0.3 — What's new</p>
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

const showVersion = ref(false)
const searchQuery = ref('')
const eAnatomyTiles = [
{ label: 'MRI Brain', icon: 'https://cdn1.imaios.com/i/images/4/2/6/0/310624-3-eng-GB/f9296ee2c558-brain3dmri.png?q=75&w=230&s=6419caa48600b24d59e4ff706f7222d3', url: 'https://www.imaios.com/en/e-anatomy/brain/mri-brain', keywords: 'neuro neurology neuroanatomy head skull cranial cerebrum cerebellum brainstem midbrain pons medulla thalamus hypothalamus basal ganglia caudate putamen globus pallidus internal capsule corpus callosum ventricles lateral ventricle third ventricle fourth ventricle CSF cerebrospinal fluid white matter gray matter cortex frontal lobe parietal lobe temporal lobe occipital lobe insula hippocampus amygdala pituitary gland sella turcica optic chiasm cranial nerves circle of willis cerebral arteries dural venous sinuses falx tentorium meninges' },
  { label: 'CT Brain', icon: 'https://cdn1.imaios.com/i/images/5/8/5/0/310585-3-eng-GB/0a8f1dd62341-brain-ct.png?q=75&w=230&s=e2205d9962bc65dab16ad83e39523668', url: 'https://www.imaios.com/en/e-anatomy/brain/ct-brain', keywords: 'neuro neurology neuroanatomy head skull cranial cerebrum cerebellum brainstem midbrain pons medulla thalamus basal ganglia caudate putamen internal capsule corpus callosum ventricles lateral ventricle third ventricle fourth ventricle CSF cerebrospinal fluid white matter gray matter cortex frontal lobe parietal lobe temporal lobe occipital lobe skull bones calvaria cranial vault sutures sinuses dural venous sinuses falx tentorium meninges hemorrhage stroke noncontrast head CT' },
  { label: 'CT Head and Neck', icon: 'https://cdn1.imaios.com/i/images/2/9/7/1/131792-4-eng-GB/caebb438ec94-Head-Neck-CT.png?q=75&w=230&s=ff1fb7b910c5e632425be1b464c46e63', url: 'https://www.imaios.com/en/e-anatomy/head-and-neck/ct-head-and-neck', keywords: 'neck throat larynx pharynx nasopharynx oropharynx hypopharynx thyroid parathyroid trachea esophagus carotid artery jugular vein vasculature lymph nodes cervical lymph nodes salivary glands parotid gland submandibular gland sublingual gland hyoid bone vocal cords epiglottis tonsils base of tongue mandible soft tissue neck ENT otolaryngology' },
  { label: 'CT Face', icon: 'https://cdn1.imaios.com/i/images/6/8/7/1/131786-5-eng-GB/e4a720e3e88b-face.png?q=75&w=230&s=abfc4f7f9c056c02ed7ebf8e8f5863ee', url: 'https://www.imaios.com/en/e-anatomy/head-and-neck/ct-face', keywords: 'facial maxillofacial sinus sinuses orbit orbits eye globe paranasal sinuses maxillary sinus frontal sinus ethmoid sinus sphenoid sinus nasal cavity nasal septum nasal bones zygoma zygomatic arch maxilla mandible jaw teeth dentition TMJ temporomandibular joint lacrimal apparatus optic nerve extraocular muscles facial trauma fracture facial bones' },
  { label: 'CT Temporal Bone', icon: 'https://cdn1.imaios.com/i/images/9/8/8/0/310889-3-eng-GB/20f6136a1276-rocher.png?q=75&w=230&s=b81e6b55fef2c059926768851a4e7555', url: 'https://www.imaios.com/en/e-anatomy/head-and-neck/ct-temporal-bone', keywords: 'ear inner ear middle ear outer ear mastoid petrous rocher otology cochlea vestibule semicircular canals ossicles malleus incus stapes external auditory canal tympanic membrane eardrum internal auditory canal facial nerve canal jugular foramen carotid canal eustachian tube temporal bone fracture hearing loss' },
  { label: 'MRI Cervical Spine', icon: 'https://cdn1.imaios.com/i/images/5/3/7/1/131735-3-eng-GB/ce0f1ce03657-cervical-spine-mri.png?q=75&w=230&s=1fa324b450d74a2b19a517f45ab2f5d3', url: 'https://www.imaios.com/en/e-anatomy/spine/mri-cervical-spine', keywords: 'neck spine c-spine cervical vertebrae cervical cord spinal cord intervertebral disc disc herniation facet joints neural foramina nerve roots ligaments atlas axis C1 C2 odontoid spinal canal stenosis myelopathy radiculopathy' },
  { label: 'MRI Lumbar Spine', icon: 'https://cdn1.imaios.com/i/images/7/8/0/0/460087-2-eng-GB/95c684afdf4e-lumbar-spine-mri.png?q=75&w=230&s=ff4c05c22f7cf136b37185de1a7db329', url: 'https://www.imaios.com/en/e-anatomy/spine/mri-lumbar-spine', keywords: 'back spine l-spine lumbar vertebrae lumbar cord conus medullaris cauda equina intervertebral disc disc herniation bulge facet joints neural foramina nerve roots ligamentum flavum spinal canal stenosis spondylolisthesis sciatica low back pain' },
  { label: 'CT Lumbar Spine', icon: 'https://cdn1.imaios.com/i/images/8/3/7/1/131738-3-eng-GB/080c9bb3cb09-lumbar-spine-ct.png?q=75&w=230&s=51f61d0d7765ccb3c5bd461d51974a8c', url: 'https://www.imaios.com/en/e-anatomy/spine/ct-lumbar-spine', keywords: 'back spine l-spine lumbar vertebrae pedicles lamina spinous process transverse process facet joints intervertebral disc spinal canal neural foramina sacrum sacroiliac joints bony anatomy fracture spondylolysis pars defect' },
  { label: 'CT Lymph Nodes', icon: 'https://cdn1.imaios.com/i/images/3/5/4/0/310453-3-eng-GB/9b48c06243a6-aires-radiotherapie.png?q=75&w=230&s=1f7bcac75a4a0a7563ace0fa47a11c56', url: 'https://www.imaios.com/en/e-anatomy/whole-body/ct-body-lymph-nodes', keywords: 'lymphatic nodal stations oncology radiotherapy body lymph node levels cervical nodes axillary nodes mediastinal nodes hilar nodes retroperitoneal nodes mesenteric nodes pelvic nodes inguinal nodes iliac nodes staging metastasis lymphoma radiation planning whole body' },
  { label: 'CT Chest', icon: 'https://cdn1.imaios.com/i/images/9/5/1/1/311159-5-eng-GB/686203b08da4-thorax.png?q=75&w=230&s=3238dc3c9b34c0477182df07e6389490', url: 'https://www.imaios.com/en/e-anatomy/thorax/ct-axial-chest', keywords: 'thorax lung lungs pulmonary thoracic heart mediastinum trachea bronchi bronchial tree pleura pleural space aorta thoracic aorta pulmonary artery pulmonary veins esophagus ribs sternum chest wall diaphragm hilum lymph nodes pericardium cardiac chambers coronary arteries' },
  { label: 'MRCP', icon: 'https://cdn1.imaios.com/i/images/5/1/3/1/311315-4-eng-GB/220cb5a0c48b-cholangiopancreatography-mr.png?q=75&w=230&s=72b7f3a4822055d81645dcc72e79dcc7', url: 'https://www.imaios.com/en/e-anatomy/abdomen-and-pelvis/magnetic-resonance-cholangiopancreatography', keywords: 'biliary pancreas pancreatic gallbladder bile duct common bile duct cystic duct hepatic ducts ampulla of vater pancreatic duct hepatobiliary body liver gallstones choledocholithiasis cholangiography magnetic resonance cholangiopancreatography' },
  { label: 'CT Abdomen and Pelvis', icon: 'https://cdn1.imaios.com/i/images/3/9/3/1/311393-4-eng-GB/1cff6e56270a-abdomen.png?q=75&w=230&s=3d7cf3818d92511c44812c86b0d7a3b7', url: 'https://www.imaios.com/en/e-anatomy/abdomen-and-pelvis/ct-axial-male-abdomen-and-pelvis', keywords: 'abdomen pelvis abdominal gi gastrointestinal body liver spleen pancreas kidneys adrenal glands stomach small bowel small intestine large bowel colon rectum bladder prostate uterus ovaries appendix aorta abdominal aorta IVC inferior vena cava mesentery retroperitoneum psoas muscle peritoneum omentum' },
  { label: 'CT Cirrhotic Abdomen and Pelvis', icon: 'https://medibyte.app/sick_abd.jpg', url: 'https://www.imaios.com/en/e-anatomy/abdomen-and-pelvis/ct-peritoneal-cavity', keywords: 'cirrhosis liver peritoneal peritoneum ascites body abdomen pelvis cirrhotic liver nodular liver portal hypertension varices splenomegaly portal vein collateral vessels peritoneal cavity peritoneal recesses paracolic gutters omentum mesentery hepatic morphology end stage liver disease' },
  { label: 'Radiograph Upper Extremity', icon: 'https://cdn1.imaios.com/i/images/0/6/5/0/310560-4-eng-GB/afc5ddc0b3b9-ms-radios.png?q=75&w=230&s=3f30892607555d5a4a0382f0e512e5c3', url: 'https://www.imaios.com/en/e-anatomy/upper-limb/radiography-upper-extremity', keywords: 'arm hand wrist elbow shoulder xray x-ray upper limb musculoskeletal MSK humerus radius ulna carpal bones metacarpals phalanges scapula clavicle glenohumeral joint forearm fingers thumb bone fracture' },
  { label: 'Radiograph Lower Extremity', icon: 'https://cdn1.imaios.com/i/images/1/1/7/1/131711-4-eng-GB/04ec3cbb1fae-membre-inferieur-radiographies.png?q=75&w=230&s=34083eee34a73622cf8903ed283c1500', url: 'https://www.imaios.com/en/e-anatomy/lower-limb/radiography-lower-extremity', keywords: 'leg knee hip xray x-ray lower limb musculoskeletal MSK femur tibia fibula pelvis acetabulum hip joint knee joint patella ankle foot bone fracture' },
  { label: 'CTA Lower Extremity', icon: 'https://cdn1.imaios.com/i/images/2/3/8/3/12723832-2-eng-GB/5f2c8a3e5e4f-lower-limb-cta.png?q=75&w=230&s=243825fed41448e7971262f9a49b23cf', url: 'https://www.imaios.com/en/e-anatomy/lower-limb/lower-limb-cta', keywords: 'angiogram angiography vascular runoff leg arteries lower limb CTA femoral artery popliteal artery anterior tibial artery posterior tibial artery peroneal artery dorsalis pedis peripheral arterial disease PAD vascular anatomy run-off study' },
  { label: 'CT Ankle and Foot', icon: 'https://cdn1.imaios.com/i/images/2/1/1/5/14435112-1-eng-GB/ca1bfee21c6b-ankle-foot-ct.png?q=75&w=230&s=6277c70aed8d1ca8dcde08c65c5d1351', url: 'https://www.imaios.com/en/e-anatomy/lower-limb/ankle-and-foot-ct', keywords: 'ankle foot podiatry tibia fibula talus calcaneus navicular cuboid cuneiforms metatarsals phalanges toes subtalar joint tarsal bones musculoskeletal MSK bone fracture' },
]

const radCallTiles = [
  { label: 'Neuroradiology', icon: 'https://rad-call.com/assets/tile-neuro.png', url: 'https://rad-call.com/app.html#neuro', keywords: 'neuro brain spine head stroke aspects mca perfusion cervical spine ligamentous trauma thoracolumbar ao rigid hematoma mandib maxillofacial neck temporal orbit sinusitis odontogenic mouth tooth peritonsillar tonsil retropharyngeal abscess epiglottitis salivary gland otitis mastoiditis lemierre angioedema infection cord dural sinus intracranial pituitary PRES shunt lumbar disc' },
  { label: 'Chest', icon: 'https://rad-call.com/assets/tile-chest.png', url: 'https://rad-call.com/app.html#chest', keywords: 'thorax lung pulmonary thoracic aorta aortic cardiac pericard diaphragm pneumonia infection pleural dissection pe embolism ild uip nsip fleischner ggo ground glass opacity edema ards aspiration' },
  { label: 'Abdomen', icon: 'https://rad-call.com/assets/tile-body.png', url: 'https://rad-call.com/app.html#body', keywords: 'abdominal pelvis gi gastrointestinal body aast liver renal kidney spleen splenic bladder rupture pancreas bowel hollow viscus pregnancy gunshot appendicitis adrenal washout ischemia obstruction gallbladder biliary diverticulitis ectopic epiploic omental infarct hernia ovarian torsion pancreatitis volvulus' },
  { label: 'MSK', icon: 'https://rad-call.com/assets/tile-msk.png', url: 'https://rad-call.com/app.html#msk', keywords: 'musculoskeletal bone joint orthopedic ortho acetabular ankle shoulder clavicle compartment humerus forearm arm elbow foot hand wrist knee tibia fibula pelvic femur humerus scapula nec fasc osteomyelitis arthritis fracture' },
  { label: 'Ultrasound', icon: 'https://rad-call.com/assets/tile-us.png', url: 'https://rad-call.com/app.html#us', keywords: 'sonography sono us doppler carotid ica stenosis adnexal cyst ovarian hydronephrosis kidney renal liver ruq transplant dvt ob retained conception retroperitoneal transplant testicular scrotal torsion tips dips' },
  { label: 'Pediatrics', icon: 'https://rad-call.com/assets/tile-peds.png', url: 'https://rad-call.com/app.html#peds', keywords: 'peds pediatric children kids lines tubes ivh intraventricular hemorrhage pyloric stenosis intussusception malrotation volvulus developmental dysplasia hip scfe slipped capital femoral epiphysis hydronephrosis kidney renal salter harris elbow critoe forearm arm fracture nat non accidental trauma' },
  { label: 'Contrast Rxn / PreMed', icon: 'https://www.acr.org/_next/image?url=https%3A%2F%2Fedge.sitecorecloud.io%2Famericancoldf5f-acrorgf92a-productioncb02-3650%2Fmedia%2FACR%2FImages%2FLogos%2F1x1%2FContrast-Manual.jpg%3Fh%3D1200%26iar%3D0%26w%3D1200&w=3200&q=75', url: 'https://rad-call.com/app.html#contrast', keywords: 'allergy allergic reaction premedication anaphylaxis iodinated contrast acr' },
  { label: 'MRI Safety', icon: 'https://www.mrisafety.com/images/BraccoLogo100.png', url: 'https://www.mrisafety.com/TMDL_list.php?orderby=alist_description', keywords: 'implant pacemaker device compatibility screening mri safety' },
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
  { label: 'Outlook', icon: 'https://upload.wikimedia.org/wikipedia/commons/thumb/c/cc/Microsoft_Outlook_Icon_%282025%E2%80%93present%29.svg/1280px-Microsoft_Outlook_Icon_%282025%E2%80%93present%29.svg.png', url: 'https://outlook.office365.com/mail/' },
  { label: 'QGenda', icon: 'https://media.glassdoor.com/sqll/648643/qgenda-squarelogo-1428511017744.png', url: 'https://app.qgenda.com/Dashboard/Company/2b10db48-c04e-4028-86ca-940a03bd9dd0' },
  { label: 'New Innovation', icon: 'https://thewetread.com/ni.png', url: 'https://www.new-innov.com/Login/Home.aspx' },

  { label: 'RADPrimer', icon: 'https://3.bp.blogspot.com/-PTnXz9zohzM/WG4SabW5-0I/AAAAAAAAI58/vmoD9bUhDQA7zxvzI9T-bQUo_BiyX1-qQCLcB/s1600/elsevier-logo.jpg', url: 'https://app.radprimer.com/curriculum' },
  { label: 'CaseStacks', icon: 'https://img.sur.ly/favicons/c/casestacks.com.ico', url: 'https://casestacks.com/dashboard' },
  { label: 'AnkiWeb', icon: 'https://images.icon-icons.com/3053/PNG/512/anki_macos_bigsur_icon_190391.png', url: 'https://ankiweb.net/decks' },
  { label: 'OpenEvidence', icon: 'https://thewetread.com/oe.png', url: 'https://www.openevidence.com/' },
  { label: 'Qualified Health', icon: 'https://www.finsmes.com/wp-content/uploads/2026/03/Qualified-Health.jpeg', url: 'https://chat.qualifiedhealthai.com/' },
  { label: 'UHS Anywhere', icon: 'https://external-content.duckduckgo.com/ip3/www.universityhealth.com.ico', url: 'https://anywhere.uhstx.com/logon/LogonPoint/index.html' },
  { label: 'Radiographics', icon: 'https://external-content.duckduckgo.com/ip3/www.rsna.org.ico', url: 'https://pubs.rsna.org/journal/radiographics' },
  { label: 'ACGME Cases', icon: 'https://thewetread.com/acgme_logo.jpg', url: 'https://apps.acgme.org/ads/CaseLogs/CaseEntry/Insert' },

// https://app.radprimer.com/curriculum


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
  content: '—';
  color: var(--accent);
  margin-right: 8px;
}
</style>
