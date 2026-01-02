<script setup>
import { ref } from 'vue'
import WelcomeScreen from './components/WelcomeScreen.vue'
import PreferenceScreen from './components/PreferenceScreen.vue'
import CalculationScreen from './components/CalculationScreen.vue'

const currentScreen = ref('welcome')
const currentStep = ref(0)
const userChoices = ref({})
const calculationData = ref(null)

const handleStart = () => {
  currentScreen.value = 'preference'
  currentStep.value = 0
}

const handleChoice = (choice) => {
  console.log('Choice made:', choice)
  userChoices.value[choice.questionId] = choice.choiceId
  
  if (currentStep.value < 3) {
    currentStep.value++
  } else {
    console.log('All choices made:', userChoices.value)
    currentScreen.value = 'calculation'
  }
}

const handleCalculate = (data) => {
  console.log('Calculation data:', data)
  calculationData.value = data
  // Future: Show results
}

const handleNoVape = () => {
  console.log('User does not vape')
  // Future: Show specific message or result
}
</script>

<template>
  <main>
    <WelcomeScreen v-if="currentScreen === 'welcome'" @start="handleStart" />
    <PreferenceScreen 
      v-else-if="currentScreen === 'preference'" 
      :step="currentStep"
      @choice="handleChoice" 
    />
    <CalculationScreen 
      v-else-if="currentScreen === 'calculation'"
      @calculate="handleCalculate"
      @no-vape="handleNoVape"
    />
  </main>
</template>

<style>
/* Global resets if needed, though WelcomeScreen is scoped */
body {
  margin: 0;
  padding: 0;
  background-color: #2563EB; /* Match WelcomeScreen bg to avoid flashes */
}
</style>

