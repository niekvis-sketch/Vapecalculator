<script setup>
import { ref, computed } from 'vue'
import muzieknootIcon from '../assets/muzieknoot.svg'
import filmIcon from '../assets/film.svg'

const props = defineProps({
  calculationData: {
    type: Object,
    required: true
  },
  userChoices: {
    type: Object,
    required: true
  }
})

const emit = defineEmits(['finish'])

const currentSlide = ref(0)

// Prices for preference items (estimates)
const itemPrices = {
  festival: 60,
  bioscoop: 15,
  sneakers: 100,
  camera: 300,
  pretpark: 45,
  dierentuin: 25,
  game: 60,
  jbl: 130
}

// Icons mapping (reusing available icons for now)
const itemIcons = {
  festival: muzieknootIcon,
  bioscoop: filmIcon,
  sneakers: filmIcon, // Placeholder
  camera: filmIcon, // Placeholder
  pretpark: muzieknootIcon, // Placeholder
  dierentuin: filmIcon, // Placeholder
  game: filmIcon, // Placeholder
  jbl: muzieknootIcon // Placeholder
}

const itemLabels = {
  festival: 'festivals',
  bioscoop: 'keer bioscoop',
  sneakers: 'paar sneakers',
  camera: 'digitale camera\'s',
  pretpark: 'keer pretpark',
  dierentuin: 'keer dierentuin',
  game: 'nieuwe games',
  jbl: 'JBL boxen'
}

// Calculations
const weeklyCost = computed(() => props.calculationData.cost * props.calculationData.amount)
const monthlyCost = computed(() => Math.round(weeklyCost.value * 52 / 12))
const yearlyCost = computed(() => weeklyCost.value * 52)
const fiveYearCost = computed(() => yearlyCost.value * 5)

// Computed choices with quantities
const computedChoices = computed(() => {
  const choices = []
  // Map the user choices to the calculation
  // userChoices is { activity: 'festival', gadget: 'sneakers', ... }
  
  Object.values(props.userChoices).forEach(choiceId => {
    if (itemPrices[choiceId]) {
      const price = itemPrices[choiceId]
      const quantity = Math.floor(yearlyCost.value / price)
      choices.push({
        id: choiceId,
        quantity: quantity,
        icon: itemIcons[choiceId],
        label: itemLabels[choiceId]
      })
    }
  })
  return choices
})

const nextSlide = () => {
  if (currentSlide.value < 2) {
    currentSlide.value++
  } else {
    emit('finish')
  }
}
</script>

<template>
  <div class="results-container">
    <div class="bg-gradient"></div>

    <!-- Slide 1: Yearly Cost -->
    <div v-if="currentSlide === 0" class="slide slide-1">
      <h2 class="title">JOUW VAPE JAAR</h2>
      
      <div class="big-number">€{{ yearlyCost }}</div>
      <p class="subtext">Geef jij uit aan vapen per jaar</p>

      <p class="connector">Dit is</p>

      <div class="stat-box">
        <span class="amount">€{{ monthlyCost }}</span>
        <span class="period">PER MAAND</span>
      </div>

      <div class="stat-box">
        <span class="amount">€{{ weeklyCost }}</span>
        <span class="period">PER WEEK</span>
      </div>
    </div>

    <!-- Slide 2: What you could have bought -->
    <div v-else-if="currentSlide === 1" class="slide slide-2">
      <h2 class="title">WAT HAD JE<br>KUNNEN KOPEN?</h2>

      <div class="comparison-grid">
        <div v-for="item in computedChoices" :key="item.id" class="comparison-card-wrapper">
          <div class="card-bg"></div>
          <div class="comparison-card">
            <div class="comp-icon">
              <img :src="item.icon" alt="" />
            </div>
            <div class="comp-number">{{ item.quantity }}</div>
            <div class="comp-label">{{ item.label }}</div>
          </div>
        </div>
      </div>
    </div>

    <!-- Slide 3: 5 Year Prediction -->
    <div v-else-if="currentSlide === 2" class="slide slide-3">
      <h2 class="title">JOUW 5 JAAR VOORSPELLING</h2>
      
      <p class="subtext-small">Als je zo doorgaat</p>
      <div class="big-number">€{{ fiveYearCost }}</div>
      <p class="subtext-small" style="margin-bottom: 2rem">in rook opgegaan...</p>

      <div class="goals-list">
        <div class="goal-item-wrapper">
          <div class="card-bg"></div>
          <div class="goal-item">
            <span class="goal-text">RIJBEWIJS</span>
            <span class="goal-icon">🚗</span>
          </div>
        </div>
        <div class="goal-item-wrapper">
          <div class="card-bg"></div>
          <div class="goal-item">
            <span class="goal-text">FATBIKE</span>
            <span class="goal-icon">🚲</span>
          </div>
        </div>
        <div class="goal-item-wrapper">
          <div class="card-bg"></div>
          <div class="goal-item">
            <span class="goal-text">LUXE VAKANTIE</span>
            <span class="goal-icon">🌴</span>
          </div>
        </div>
      </div>

      <button class="finish-btn" @click="$emit('finish')">
        ROND JE BEREKENING AF
      </button>
    </div>

    <!-- Navigation Arrow (only for first 2 slides) -->
    <button v-if="currentSlide < 2" class="next-btn-wrapper" @click="nextSlide">
      <div class="btn-bg"></div>
      <div class="btn-face">
        <svg viewBox="0 0 24 24" fill="currentColor">
          <path d="M12 4l-1.41 1.41L16.17 11H4v2h12.17l-5.58 5.59L12 20l8-8z"/>
        </svg>
      </div>
    </button>

  </div>
</template>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Inter:ital,wght@1,900&display=swap');

.results-container {
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
  overflow-x: hidden;
}

.bg-gradient {
  position: absolute;
  inset: 0;
  background: linear-gradient(180deg, rgba(217, 249, 4, 0.1) 0%, transparent 30%, transparent 70%, rgba(217, 249, 4, 0.2) 100%);
  pointer-events: none;
  z-index: 0;
}

.slide {
  position: relative;
  z-index: 10;
  width: 100%;
  max-width: 400px;
  display: flex;
  flex-direction: column;
  align-items: center;
  padding-top: 40px;
  padding-bottom: 100px; /* Space for bottom button */
  animation: fadeIn 0.5s ease-out;
}

@keyframes fadeIn {
  from { opacity: 0; transform: translateY(20px); }
  to { opacity: 1; transform: translateY(0); }
}

.title {
  font-style: italic;
  font-weight: 900;
  font-size: 2rem;
  color: #1E3A8A;
  text-transform: uppercase;
  text-align: center;
  margin-bottom: 1rem;
  line-height: 1.1;
}

.big-number {
  font-family: 'Inter', sans-serif;
  font-weight: 900;
  font-style: italic;
  font-size: 5rem;
  color: #1E3A8A;
  text-shadow: 4px 4px 0px rgba(0,0,0,0.1);
  margin: 1rem 0;
  line-height: 1;
}

.subtext {
  font-family: 'Pacifico', cursive; /* Or Inter Italic based on image? Image looks like script/handwritten for "Geef jij uit..."? No, looks like bold italic sans. Let's stick to Inter Bold Italic */
  font-family: 'Inter', sans-serif;
  font-weight: 700;
  font-style: italic;
  color: #1E3A8A;
  margin-bottom: 2rem;
  text-align: center;
}

.subtext-small {
  font-family: 'Inter', sans-serif;
  font-weight: 700;
  font-style: italic;
  color: #1E3A8A;
  margin: 0;
}

.connector {
  font-family: 'Inter', sans-serif;
  font-weight: 700;
  font-style: italic;
  color: #1E3A8A;
  margin-bottom: 1rem;
}

/* Stat Boxes (Slide 1) */
.stat-box {
  width: 100%;
  background-color: #E5E7EB;
  border: 1px solid black;
  padding: 20px;
  margin-bottom: 1.5rem;
  display: flex;
  justify-content: space-between;
  align-items: center;
  box-shadow: 4px 4px 0px #D9F904; /* Yellow shadow */
}

.stat-box .amount {
  font-size: 3rem;
  font-weight: 900;
  font-style: italic;
  color: #1E3A8A;
}

.stat-box .period {
  font-size: 1.2rem;
  font-weight: 900;
  font-style: italic;
  color: #1E3A8A;
  text-transform: uppercase;
}

/* Comparison Grid (Slide 2) */
.comparison-grid {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 20px;
  width: 100%;
}

.comparison-card-wrapper {
  position: relative;
  width: 100%;
  aspect-ratio: 1;
}

.comparison-card-wrapper .card-bg {
  position: absolute;
  top: 6px;
  left: -6px;
  width: 100%;
  height: 100%;
  background-color: #D9F904;
  border: 1px solid black;
  z-index: 0;
}

.comparison-card {
  position: relative;
  width: 100%;
  height: 100%;
  background-color: #E5E7EB;
  border: 1px solid black;
  z-index: 1;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  padding: 10px;
}

.comp-icon {
  width: 40px;
  height: 40px;
  margin-bottom: 5px;
}

.comp-icon img {
  width: 100%;
  height: 100%;
  object-fit: contain;
}

.comp-number {
  font-size: 3rem;
  font-weight: 900;
  font-style: italic;
  color: #1E3A8A;
  line-height: 1;
}

.comp-label {
  font-size: 0.9rem;
  font-weight: 700;
  font-style: italic;
  color: #1E3A8A;
  text-align: center;
  line-height: 1.1;
}

/* Goals List (Slide 3) */
.goals-list {
  width: 100%;
  display: flex;
  flex-direction: column;
  gap: 20px;
  margin-bottom: 3rem;
}

.goal-item-wrapper {
  position: relative;
  width: 100%;
  height: 60px;
}

.goal-item-wrapper .card-bg {
  position: absolute;
  top: 4px;
  left: 4px; /* Shadow to bottom-right in image? Actually looks like standard offset */
  width: 100%;
  height: 100%;
  background-color: #D9F904; /* Or just shadow? Image has yellow fill inside? No, looks like grey box with yellow shadow? Actually image 3 has grey boxes with yellow shadow underneath */
  background-color: transparent; /* Wait, image 3 items are grey boxes with yellow shadow? No, they are grey boxes with yellow shadow underneath. */
  background-color: #D9F904;
  border: 1px solid black;
  z-index: 0;
  left: 0;
  top: 4px;
  left: 4px;
}

.goal-item {
  position: relative;
  width: 100%;
  height: 100%;
  background-color: #E5E7EB;
  border: 1px solid black;
  z-index: 1;
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 0 20px;
}

.goal-text {
  font-size: 1.5rem;
  font-weight: 900;
  font-style: italic;
  color: #1E3A8A;
}

.goal-icon {
  font-size: 1.5rem;
  color: #1E3A8A;
}

/* Navigation Button */
.next-btn-wrapper {
  position: fixed;
  bottom: 40px;
  width: 100px;
  height: 60px;
  background: none;
  border: none;
  padding: 0;
  cursor: pointer;
  z-index: 100;
}

.next-btn-wrapper .btn-bg {
  position: absolute;
  top: 6px;
  left: 6px;
  width: 100%;
  height: 100%;
  background-color: #E5E7EB;
  border: 1px solid black;
  z-index: 0;
}

.next-btn-wrapper .btn-face {
  position: relative;
  width: 100%;
  height: 100%;
  background-color: #D9F904;
  border: 1px solid black;
  z-index: 1;
  display: flex;
  align-items: center;
  justify-content: center;
}

.next-btn-wrapper svg {
  width: 32px;
  height: 32px;
  color: black;
}

.finish-btn {
  background-color: #D9F904;
  border: 1px solid black;
  padding: 16px 32px;
  font-family: 'Inter', sans-serif;
  font-weight: 900;
  font-style: italic;
  font-size: 1.2rem;
  cursor: pointer;
  box-shadow: 4px 4px 0px rgba(0,0,0,0.1);
  text-transform: uppercase;
}
</style>
