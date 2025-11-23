<script setup>
import { ref, computed } from 'vue'
import InputPanel from './components/InputPanel.vue'
import LoadingOverlay from './components/LoadingOverlay.vue'
import WrappedShow from './components/WrappedShow.vue'

const weeklyVapes = ref(2)
const costPerVape = ref(6.00)
const age = ref('')
const yearsVaping = ref('')

const weeklyCost = computed(() => weeklyVapes.value * costPerVape.value)
const yearlyCost = computed(() => weeklyCost.value * 52)
const monthlyCost = computed(() => yearlyCost.value / 12)

const comparisons = [
  { id: 'bioscoop', name: 'Bioscoopkaartjes', cost: 12, icon: '🎬' },
  { id: 'mcd', name: "McDonald's menu's", cost: 9, icon: '🍔' },
  { id: 'spotify', name: 'Maanden Spotify Premium', cost: 11, icon: '🎵' },
  { id: 'netflix', name: 'Maanden Netflix', cost: 11, icon: '📺' },
  { id: 'sneakers', name: 'Paar Sneakers', cost: 100, icon: '👟' },
  { id: 'game', name: 'Nieuwe Games', cost: 60, icon: '🎮' }
]

const view = ref('ask') // 'ask' | 'loading' | 'wrapped'

function startWrapped(){
  view.value = 'loading'
  // simulate computation/loading then show wrapped test voor mac 
  setTimeout(()=>{ view.value = 'wrapped' }, 1500)
}

function closeWrapped(){ view.value = 'ask' }

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
    </div>

    <transition name="fade-slide" mode="out-in">
      <header v-if="view === 'ask'" class="header-animate">
        <h1>Vape Calculator</h1>
        <p>Ontdek wat jouw vape-gewoonte écht kost in geld, tijd en gezondheid.</p>
      </header>
    </transition>

    <main class="app-grid" :class="{ 'full-width': view === 'wrapped' }">
      <transition name="zoom-fade" mode="out-in">
        <aside v-if="view === 'ask'" class="input-container glass" key="ask">
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
          />
        </aside>

        <section v-else-if="view === 'wrapped'" class="wrapped-container" key="wrapped">
          <WrappedShow 
            :weeklyVapes="weeklyVapes"
            :costPerVape="costPerVape"
            :weeklyCost="weeklyCost" 
            :monthlyCost="monthlyCost" 
            :yearlyCost="yearlyCost" 
            :comparisons="comparisons"
            :age="age"
            :yearsVaping="yearsVaping"
            @close="closeWrapped" 
          />
        </section>

        <LoadingOverlay v-else-if="view === 'loading'" key="loading" />
      </transition>
    </main>
  </div>
</template>

<style scoped>
.full-width { display: block; width: 100%; }
.wrapped-container { width: 100%; height: 100%; }
.input-container { 
  width: 100%; 
  max-width: 500px; 
  margin: 0 auto; 
  padding: 40px;
  border-radius: var(--radius);
}

.header-animate {
  animation: slide-up 0.8s cubic-bezier(0.16, 1, 0.3, 1);
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
        
