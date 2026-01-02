<script setup>
import { ref, computed } from 'vue'
import muzieknootIcon from '../assets/muzieknoot.svg'
import filmIcon from '../assets/film.svg'
// Placeholder icons for new options - using existing ones for now or generic
// In a real scenario, we'd import specific SVGs for sneakers, camera, etc.
// For now, I'll reuse the existing ones to prevent errors, but the logic is ready for new assets.

const props = defineProps({
  step: {
    type: Number,
    default: 0
  }
})

const emit = defineEmits(['choice'])

const questions = [
  {
    id: 'activity',
    title: 'WAT DOE JIJ\nLIEVER?',
    options: [
      { id: 'festival', label: 'festival', icon: muzieknootIcon, font: 'script-font' },
      { id: 'bioscoop', label: 'Bioscoop', icon: filmIcon, font: 'bold-font' }
    ]
  },
  {
    id: 'gadget',
    title: 'WAT HEB JIJ\nLIEVER?',
    options: [
      { id: 'sneakers', label: 'Nieuwe sneakers', icon: filmIcon, font: 'bold-font' }, // Placeholder icon
      { id: 'camera', label: 'Digitale camera', icon: muzieknootIcon, font: 'bold-font' } // Placeholder icon
    ]
  },
  {
    id: 'outing',
    title: 'WAAR GA JIJ\nHEEN?',
    options: [
      { id: 'pretpark', label: 'Pretpark', icon: muzieknootIcon, font: 'script-font' }, // Placeholder icon
      { id: 'dierentuin', label: 'Dierentuin', icon: filmIcon, font: 'bold-font' } // Placeholder icon
    ]
  },
  {
    id: 'entertainment',
    title: 'WAT WIL JIJ\nHEBBEN?',
    options: [
      { id: 'game', label: 'Nieuwe game', icon: filmIcon, font: 'bold-font' }, // Placeholder icon
      { id: 'jbl', label: 'Nieuwe JBL box', icon: muzieknootIcon, font: 'bold-font' } // Placeholder icon
    ]
  }
]

const currentQuestion = computed(() => questions[props.step] || questions[0])

const selectChoice = (choiceId) => {
  emit('choice', { 
    questionId: currentQuestion.value.id, 
    choiceId: choiceId 
  })
}
</script>

<template>
  <div class="choice-container">
    <!-- Top Pattern Bar -->
    <div class="pattern-bar top">
    </div>

    <div class="content">
      <h2 class="title" style="white-space: pre-line">{{ currentQuestion.title }}</h2>

      <div class="cards">
        <button 
          v-for="option in currentQuestion.options" 
          :key="option.id"
          class="card-wrapper" 
          @click="selectChoice(option.id)"
        >
          <div class="card-bg"></div>
          <div class="card">
            <div class="icon">
              <img :src="option.icon" :alt="option.label" />
            </div>
            <span :class="['label', option.font]">{{ option.label }}</span>
          </div>
        </button>
      </div>
    </div>

    <!-- Bottom Pattern Bar -->
    <div class="pattern-bar bottom">
    </div>
  </div>
</template>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Inter:ital,wght@1,900&family=Pacifico&display=swap');

.choice-container {
  display: flex;
  flex-direction: column;
  height: 100vh;
  width: 100%;
  background-color: #E5E7EB; /* Light gray background */
  font-family: 'Inter', sans-serif;
  overflow: hidden;
}

.pattern-bar {
  height: 15vh;
  width: 100%;
  background: linear-gradient(180deg, #D9F904 0%, #E5E7EB 100%);
  position: relative;
  overflow: hidden;
}

.pattern-bar.bottom {
  background: linear-gradient(0deg, #D9F904 0%, #E5E7EB 100%);
  margin-top: auto;
}

.content {
  flex: 1;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  padding: 20px;
  z-index: 10;
}

.title {
  font-style: italic;
  font-weight: 900;
  font-size: 2.5rem;
  line-height: 1.1;
  text-align: center;
  color: #1E3A8A; /* Dark Blue */
  text-transform: uppercase;
  margin-bottom: 3rem;
}

.cards {
  display: flex;
  gap: 2rem;
  justify-content: center;
  width: 100%;
  max-width: 600px;
}

.card-wrapper {
  position: relative;
  width: 160px;
  height: 240px;
  background: none;
  border: none;
  padding: 0;
  cursor: pointer;
  transition: transform 0.1s;
}

.card-wrapper:active {
  transform: translate(2px, 2px);
}

.card-wrapper:active .card-bg {
  transform: translate(0, 0);
}

.card-bg {
  position: absolute;
  top: 8px;
  left: -8px;
  width: 100%;
  height: 100%;
  background-color: #D9F904; /* Neon Yellow */
  border: 1px solid black;
  z-index: 0;
  transition: transform 0.1s;
}

.card {
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
  padding: 20px;
}

.icon {
  width: 80px;
  height: 80px;
  margin-bottom: 2rem;
  display: flex;
  align-items: center;
  justify-content: center;
}

.icon img {
  width: 100%;
  height: 100%;
  object-fit: contain;
}

.label {
  font-size: 1.5rem;
  color: #2563EB;
}

.script-font {
  font-family: 'Pacifico', cursive;
  font-weight: 400;
}

.bold-font {
  font-family: 'Inter', sans-serif;
  font-weight: 900;
  font-style: italic;
  text-transform: lowercase;
}

/* Mobile Responsiveness */
@media (max-width: 600px) {
  .title {
    font-size: 2rem;
    margin-bottom: 2rem;
  }
  
  .cards {
    gap: 1.5rem;
  }

  .card-wrapper {
    width: 140px;
    height: 200px;
  }
  
  .icon {
    width: 60px;
    height: 60px;
  }
  
  .label {
    font-size: 1.2rem;
  }
}
</style>
