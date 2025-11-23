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
    <header v-if="view === 'ask'">
      <h1>Vape Calculator</h1>
      <p>Ontdek wat jouw vape-gewoonte écht kost in geld, tijd en gezondheid.</p>
    </header>

    <main class="app-grid" :class="{ 'full-width': view === 'wrapped' }">
      <aside v-if="view === 'ask'" class="input-container">
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

      <section v-if="view === 'wrapped'" class="wrapped-container">
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

      <LoadingOverlay v-if="view === 'loading'" />
    </main>
  </div>
</template>

<style scoped>
.full-width { display: block; }
.wrapped-container { width: 100%; height: 100%; }
.input-container { width: 100%; max-width: 500px; margin: 0 auto; }
</style>
        
