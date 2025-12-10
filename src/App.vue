<script setup>
import { ref, computed, watch } from 'vue'
import InputPanel from './components/InputPanel.vue'
import LoadingOverlay from './components/LoadingOverlay.vue'
import WrappedShow from './components/WrappedShow.vue'
import SettingsPanel from './components/SettingsPanel.vue'
import WelcomeScreen from './components/WelcomeScreen.vue'
import PreferenceScreen from './components/PreferenceScreen.vue'

const weeklyVapes = ref(2)
const costPerVape = ref(6.00)
const age = ref('')
const yearsVaping = ref('')

const showSettings = ref(false)
const defaultSettings = {
  costs: true,
  comparisons: true,
  time: true,
  health: true,
  future: true,
  stats: true,
  savings: true,
  positive: true,
  action: true
}

const savedSettings = localStorage.getItem('vapecalc_settings')
const slideSettings = ref(savedSettings ? JSON.parse(savedSettings) : defaultSettings)

watch(slideSettings, (newVal) => {
  localStorage.setItem('vapecalc_settings', JSON.stringify(newVal))
}, { deep: true })

// Theme Management
const themes = ref({
  default: {
    name: 'Standaard (Dark)',
    colors: {
      '--bg': '#0f172a',
      '--surface': 'rgba(30, 41, 59, 0.7)',
      '--surface-highlight': 'rgba(51, 65, 85, 0.8)',
      '--primary': '#818cf8',
      '--primary-glow': 'rgba(129, 140, 248, 0.5)',
      '--secondary': '#c084fc',
      '--accent': '#2dd4bf',
      '--text-main': '#f8fafc',
      '--text-muted': '#94a3b8',
      '--border': 'rgba(148, 163, 184, 0.1)',
      '--bg-pattern': 'radial-gradient(circle at 15% 50%, rgba(79, 70, 229, 0.15) 0%, transparent 25%), radial-gradient(circle at 85% 30%, rgba(192, 132, 252, 0.15) 0%, transparent 25%)'
    }
  },
  pink: {
    name: 'Pink Paradise 🌸',
    colors: {
      '--bg': '#380819',
      '--surface': 'rgba(80, 10, 40, 0.7)',
      '--surface-highlight': 'rgba(100, 20, 50, 0.8)',
      '--primary': '#f472b6',
      '--primary-glow': 'rgba(244, 114, 182, 0.5)',
      '--secondary': '#fb7185',
      '--accent': '#fbcfe8',
      '--text-main': '#fff1f2',
      '--text-muted': '#fda4af',
      '--border': 'rgba(251, 207, 232, 0.1)',
      '--bg-pattern': 'radial-gradient(circle at 15% 50%, rgba(244, 114, 182, 0.15) 0%, transparent 25%), radial-gradient(circle at 85% 30%, rgba(251, 113, 133, 0.15) 0%, transparent 25%)'
    }
  },
  neon: {
    name: 'Neon Fintech ⚡',
    colors: {
      '--bg': '#050A14',
      '--surface': 'rgba(15, 23, 42, 0.8)',
      '--surface-highlight': 'rgba(30, 41, 59, 0.8)',
      '--primary': '#D4FF00',
      '--primary-glow': 'rgba(212, 255, 0, 0.4)',
      '--secondary': '#0ea5e9',
      '--accent': '#D4FF00',
      '--text-main': '#ffffff',
      '--text-muted': '#94a3b8',
      '--border': 'rgba(212, 255, 0, 0.2)',
      '--bg-pattern': 'linear-gradient(rgba(255, 255, 255, 0.03) 1px, transparent 1px), linear-gradient(90deg, rgba(255, 255, 255, 0.03) 1px, transparent 1px), radial-gradient(circle at 50% 50%, rgba(21, 94, 89, 0.15) 0%, transparent 50%)'
    }
  },
  editorial: {
    name: 'Editorial Retro 🗞️',
    colors: {
      '--bg': '#121212',
      '--surface': '#1E1E1E',
      '--surface-highlight': '#2A2A2A',
      '--primary': '#BEF264',
      '--primary-glow': 'transparent',
      '--secondary': '#C4B5FD',
      '--accent': '#F87171',
      '--text-main': '#F5F5F0',
      '--text-muted': '#A3A3A3',
      '--border': '#F5F5F0',
      '--radius': '0px',
      '--bg-pattern': 'radial-gradient(#333 1px, transparent 1px)'
    }
  }
})

const savedTheme = localStorage.getItem('vapecalc_theme')
const currentTheme = ref(savedTheme && themes.value[savedTheme] ? savedTheme : 'default')

function applyTheme(themeKey) {
  const theme = themes.value[themeKey]
  if (!theme) return
  
  const root = document.documentElement
  root.setAttribute('data-theme', themeKey)
  
  Object.entries(theme.colors).forEach(([key, value]) => {
    root.style.setProperty(key, value)
  })
}

// Apply initial theme
applyTheme(currentTheme.value)

watch(currentTheme, (newVal) => {
  localStorage.setItem('vapecalc_theme', newVal)
  applyTheme(newVal)
})

const weeklyCost = computed(() => weeklyVapes.value * costPerVape.value)
const yearlyCost = computed(() => weeklyCost.value * 52)
const monthlyCost = computed(() => yearlyCost.value / 12)

// Wizard State
const wizardStep = ref(0) // 0=Welcome, 1-3=Prefs, 4=Input, 5=Loading, 6=Wrapped
const userPreferences = ref([])

const preferenceQuestions = [
  {
    title: "Wat doe je liever?",
    options: [
      { id: 'cinema', name: 'Bioscoop', cost: 12, icon: '🎬' },
      { id: 'concert', name: 'Concert/Festival', cost: 60, icon: '🎵' }
    ]
  },
  {
    title: "Waar word je blijer van?",
    options: [
      { id: 'sneakers', name: 'Nieuwe Sneakers', cost: 120, icon: '👟' },
      { id: 'gaming', name: 'Gaming Gear', cost: 80, icon: '🎮' }
    ]
  },
  {
    title: "Ideaal weekend?",
    options: [
      { id: 'weekend', name: 'Weekendje Weg', cost: 200, icon: '✈️' },
      { id: 'dinner', name: 'Uit Eten', cost: 25, icon: '🍕' }
    ]
  }
]

function startWizard() {
  wizardStep.value = 1
}

function handlePreferenceSelect(option) {
  userPreferences.value.push(option)
  if (wizardStep.value < 3) {
    wizardStep.value++
  } else {
    wizardStep.value = 4 // Go to Input
  }
}

function startWrapped(){
  if (costPerVape.value === 0) {
    // view.value = 'bart' // Removed Bart for now or handle differently
    return
  }
  wizardStep.value = 5 // Loading
  // simulate computation/loading then show wrapped test voor mac 
  setTimeout(()=>{ wizardStep.value = 6 }, 1500)
}

function closeWrapped(){ 
  wizardStep.value = 0 
  userPreferences.value = []
}

function handleWeeklyUpdate(val){ weeklyVapes.value = Number(val) }
function handleCostUpdate(val){ costPerVape.value = Number(val) }
function handleAgeUpdate(val){ age.value = val }
function handleYearsUpdate(val){ yearsVaping.value = val }
</script>

<template>
  <div class="container">
    <div class="bg-blobs">
      <div class="blob blob-1"></div>
      <div class="blob blob-2"></div>
      <div class="floating-euro">
        <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor">
          <path d="M15 18.5c-2.5 0-4.5-1.5-5.5-4h5.5v-2h-6c0-0.5 0-1 0-1.5s0-1 0-1.5h6v-2h-5.5c1-2.5 3-4 5.5-4 1.5 0 3 0.5 4 1.5l1.5-1.5c-1.5-1.5-3.5-2-5.5-2-4 0-7 3-7.5 7h-2.5v2h2c0 0.5 0 1 0 1.5s0 1 0 1.5h-2v2h2.5c0.5 4 3.5 7 7.5 7 2 0 4-0.5 5.5-2l-1.5-1.5c-1-1-2.5-1.5-4-1.5z"/>
        </svg>
      </div>
    </div>

    <transition name="fade-slide" mode="out-in">
      <header v-if="wizardStep === 4" class="header-animate">
        <h1>Vape Calculator</h1>
        <p>Ontdek wat jouw vape-gewoonte écht kost in geld, tijd en gezondheid.</p>
      </header>
    </transition>

    <SettingsPanel 
      v-if="showSettings" 
      v-model="slideSettings" 
      v-model:theme="currentTheme"
      :themes="themes"
      @close="showSettings = false" 
    />

    <main class="app-grid" :class="{ 'full-width': wizardStep === 6 }">
      <transition name="zoom-fade" mode="out-in">
        
        <!-- Step 0: Welcome -->
        <WelcomeScreen 
          v-if="wizardStep === 0" 
          key="welcome"
          @start="startWizard"
        />

        <!-- Step 1-3: Preferences -->
        <PreferenceScreen
          v-else-if="wizardStep >= 1 && wizardStep <= 3"
          :key="`pref-${wizardStep}`"
          :question="preferenceQuestions[wizardStep - 1]"
          :step="wizardStep"
          :totalSteps="3"
          @select="handlePreferenceSelect"
        />

        <!-- Step 4: Input -->
        <aside v-else-if="wizardStep === 4" class="input-container glass" key="input">
          <InputPanel
            :weeklyVapes="weeklyVapes"
            :costPerVape="costPerVape"
            :age="age"
            :yearsVaping="yearsVaping"
            @update:weeklyVapes="handleWeeklyUpdate"
            @update:costPerVape="handleCostUpdate"
            @update:age="handleAgeUpdate"
            @update:yearsVaping="handleYearsUpdate"
            @start="startWrapped"
            @open-settings="showSettings = true"
          />
        </aside>

        <!-- Step 6: Wrapped -->
        <section v-else-if="wizardStep === 6" class="wrapped-container" key="wrapped">
          <WrappedShow 
            :weeklyVapes="weeklyVapes"
            :costPerVape="costPerVape"
            :weeklyCost="weeklyCost" 
            :monthlyCost="monthlyCost" 
            :yearlyCost="yearlyCost" 
            :comparisons="userPreferences"
            :age="age"
            :yearsVaping="yearsVaping"
            :slideSettings="slideSettings"
            @close="closeWrapped" 
          />
        </section>

        <LoadingOverlay v-else-if="wizardStep === 5" key="loading" />

      </transition>
    </main>
  </div>
</template>

<style scoped>
.full-width { display: block; width: 100%; }
.wrapped-container { width: 100%; height: 100%; }
.input-container { 
  width: 100%; 
  max-width: 600px; 
  margin: 0 auto; 
  padding: 40px;
  border-radius: var(--radius);
}

.bart-message {
  text-align: center;
  padding: 40px;
  border-radius: var(--radius);
  max-width: 500px;
  margin: 0 auto;
}

.bart-message h2 {
  font-size: 2rem;
  margin-bottom: 16px;
  color: #f87171;
}

.bart-message p {
  font-size: 1.2rem;
  margin-bottom: 32px;
  color: var(--text-muted);
}

.header-animate {
  animation: slide-up 0.8s cubic-bezier(0.16, 1, 0.3, 1);
  position: relative;
}

.settings-trigger {
  position: absolute;
  top: 0;
  right: 0;
  background: rgba(255, 255, 255, 0.1);
  border: none;
  border-radius: 50%;
  width: 40px;
  height: 40px;
  font-size: 1.2rem;
  cursor: pointer;
  transition: all 0.2s;
  display: flex;
  align-items: center;
  justify-content: center;
}

.settings-trigger:hover {
  background: rgba(255, 255, 255, 0.2);
  transform: rotate(45deg);
}

/* Background Blobs */
.bg-blobs {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  z-index: -1;
  overflow: hidden;
  pointer-events: none;
}

.blob {
  position: absolute;
  border-radius: 50%;
  filter: blur(80px);
  opacity: 0.4;
  animation: float 10s infinite ease-in-out;
}

.blob-1 {
  top: -10%;
  left: -10%;
  width: 50vw;
  height: 50vw;
  background: var(--primary);
  animation-delay: 0s;
}

.blob-2 {
  bottom: -10%;
  right: -10%;
  width: 60vw;
  height: 60vw;
  background: var(--secondary);
  animation-delay: -5s;
}

.floating-euro {
  position: absolute;
  top: 50%;
  left: 75%;
  transform: translate(-50%, -50%);
  width: 80vh;
  height: 80vh;
  opacity: 0.05;
  pointer-events: none;
  z-index: 0;
  color: var(--text);
  animation: floatEuro 6s ease-in-out infinite;
}

.floating-euro svg {
  width: 100%;
  height: 100%;
}

@keyframes floatEuro {
  0%, 100% {
    transform: translate(-50%, -50%) translateY(0);
  }
  50% {
    transform: translate(-50%, -50%) translateY(-20px);
  }
}

/* Transitions */
.fade-slide-enter-active,
.fade-slide-leave-active {
  transition: all 0.5s ease;
}
.fade-slide-enter-from,
.fade-slide-leave-to {
  opacity: 0;
  transform: translateY(-20px);
}

.zoom-fade-enter-active,
.zoom-fade-leave-active {
  transition: all 0.6s cubic-bezier(0.16, 1, 0.3, 1);
}
.zoom-fade-enter-from,
.zoom-fade-leave-to {
  opacity: 0;
  transform: scale(0.95) translateY(20px);
}
</style>
        
