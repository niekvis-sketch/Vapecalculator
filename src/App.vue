<script setup>
import WelcomeScreen from './components/WelcomeScreen.vue'

const handleStart = () => {
  console.log('Start clicked - Flow to be implemented')
  // Hier komt later de logica voor de volgende stap
}
</script>

<template>
  <main>
    <WelcomeScreen @start="handleStart" />
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

