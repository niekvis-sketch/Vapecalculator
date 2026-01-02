<script setup>
import { ref } from 'vue'
import WelcomeScreen from './components/WelcomeScreen.vue'
import PreferenceScreen from './components/PreferenceScreen.vue'

const currentScreen = ref('welcome')
const currentStep = ref(0)
const userChoices = ref({})

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
    // Hier komt de logica voor het volgende scherm (bijv. resultaten)
  }
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

