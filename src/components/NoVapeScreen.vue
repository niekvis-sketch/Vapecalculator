<script setup>
import { ref } from 'vue'

const emit = defineEmits(['calculate'])

const vapeCost = 12
const vapesPerWeek = 1

const handleCalculate = () => {
  emit('calculate', { cost: vapeCost, amount: vapesPerWeek })
}
</script>

<template>
  <div class="calc-container">
    <!-- Gradient Background -->
    <div class="bg-gradient"></div>

    <div class="content">
      <h2 class="title">WAT KOST JOUW VAPE?</h2>

      <div class="main-card-wrapper">
        <div class="card-bg"></div>
        <div class="card">
          <p class="instruction">
            DEZE BEREKENING IS GEBASEERD OP EEN VAPE VAN €12 EN EEN GEBRUIK VAN 1 VAPE PER WEEK.
          </p>

          <!-- Cost Section -->
          <div class="input-section">
            <label>HOEVEEL KOST JE VAPE</label>
            <div class="controls">
              <div class="value-display">€{{ vapeCost }}</div>
            </div>
          </div>

          <!-- Amount Section -->
          <div class="input-section">
            <label>AANTAL VAPES PER WEEK</label>
            <div class="controls">
              <div class="value-display">{{ vapesPerWeek }}</div>
            </div>
          </div>
        </div>
      </div>

      <button class="calculate-btn-wrapper" @click="handleCalculate">
        <div class="btn-bg"></div>
        <div class="btn-face">BEREKEN RESULTAAT</div>
      </button>
    </div>
  </div>
</template>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Inter:ital,wght@1,900&display=swap');

.calc-container {
  position: relative;
  width: 100%;
  min-height: 100vh;
  background-color: #E5E7EB;
  font-family: 'Inter', sans-serif;
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 20px;
  overflow-y: auto;
  padding-top: 40px;
  padding-bottom: 40px;
}

.bg-gradient {
  position: absolute;
  inset: 0;
  background: linear-gradient(180deg, rgba(217, 249, 4, 0.1) 0%, transparent 30%, transparent 70%, rgba(217, 249, 4, 0.2) 100%);
  pointer-events: none;
}

.content {
  position: relative;
  z-index: 10;
  width: 100%;
  max-width: 400px;
}

.title {
  font-style: italic;
  font-weight: 900;
  font-size: 2rem;
  color: #1E3A8A;
  text-transform: uppercase;
  text-align: center;
  margin-bottom: 2rem;
  line-height: 1.1;
}

.main-card-wrapper {
  position: relative;
  width: 100%;
  margin-bottom: 4rem;
}

.card-bg {
  position: absolute;
  top: 10px;
  left: -10px;
  width: 100%;
  height: 100%;
  background-color: #D9F904;
  border: 1px solid black;
  z-index: 0;
}

.card {
  position: relative;
  background-color: #E5E7EB;
  border: 1px solid black;
  padding: 30px 20px;
  z-index: 1;
  display: flex;
  flex-direction: column;
  gap: 2rem;
}

.instruction {
  color: #1E3A8A;
  font-weight: 900;
  font-style: italic;
  font-size: 0.8rem;
  margin: 0;
  margin-bottom: 0.5rem;
  text-transform: uppercase;
}

.input-section label {
  display: block;
  color: #1E3A8A;
  font-weight: 900;
  font-style: italic;
  font-size: 1rem;
  margin-bottom: 0.5rem;
  text-transform: uppercase;
}

.controls {
  display: flex;
  align-items: center;
  gap: 15px;
}

.value-display {
  flex: 1;
  background-color: #E5E7EB;
  border: 1px solid #9CA3AF;
  padding: 10px;
  font-size: 2.5rem;
  font-weight: 900;
  font-style: italic;
  color: #1E3A8A;
  text-align: center;
  min-width: 100px;
  box-shadow: inset 0 2px 4px rgba(0,0,0,0.05);
}

/* Calculate Button Wrapper */
.calculate-btn-wrapper {
  position: relative;
  width: 100%;
  height: 60px;
  background: none;
  border: none;
  padding: 0;
  cursor: pointer;
  transition: transform 0.1s;
}

.calculate-btn-wrapper:active {
  transform: translate(2px, 2px);
}

.calculate-btn-wrapper:active .btn-bg {
  transform: translate(0, 0);
}

.btn-bg {
  position: absolute;
  top: 6px;
  left: -6px;
  width: 100%;
  height: 100%;
  background-color: #E5E7EB; /* Grey shadow/bg */
  border: 1px solid black;
  z-index: 0;
  transition: transform 0.1s;
}

.btn-face {
  position: relative;
  width: 100%;
  height: 100%;
  background-color: #D9F904;
  border: 1px solid black;
  z-index: 1;
  display: flex;
  align-items: center;
  justify-content: center;
  font-family: 'Inter', sans-serif;
  font-weight: 900;
  font-style: italic;
  font-size: 1.2rem;
  text-transform: uppercase;
}
</style>
