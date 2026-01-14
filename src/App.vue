<script setup>
import { ref } from 'vue'
import WelcomeScreen from './components/WelcomeScreen.vue'
import PreferenceScreen from './components/PreferenceScreen.vue'
import CalculationScreen from './components/CalculationScreen.vue'
import NoVapeScreen from './components/NoVapeScreen.vue'
import ResultsWrapped from './components/ResultsWrapped.vue'

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
  currentScreen.value = 'results'
}

const handleNoVape = () => {
  console.log('User does not vape')
  currentScreen.value = 'no-vape'
}

const handleFinish = () => {
  // Reset or show final CTA
  location.reload() // Simple reset for now
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
    <NoVapeScreen
      v-else-if="currentScreen === 'no-vape'"
      @calculate="handleCalculate"
    />
    <ResultsWrapped
      v-else-if="currentScreen === 'results'"
      :calculation-data="calculationData"
      :user-choices="userChoices"
      @finish="handleFinish"
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

