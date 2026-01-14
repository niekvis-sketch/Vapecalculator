<script setup>
import { ref, computed } from 'vue'
import muzieknootIcon from '../assets/muzieknoot.svg'
import filmIcon from '../assets/film.svg'
import sneakerIcon from '../assets/sneaker.svg'
import cameraIcon from '../assets/camera.svg'
import themeparkIcon from '../assets/themepark.svg'
import zooIcon from '../assets/zoo.svg'
import gameIcon from '../assets/game.svg'
import bluetoothIcon from '../assets/bluetooth.svg'

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
    if (currentSlide.value === 2) {
      startRevealAnimation()
    }
  } else {
    emit('finish')
  }
}

// Reveal Animation Logic
const isRevealing = ref(false)
const showResult = ref(false)
const animationPhase = ref('idle') // idle, buildup, energy, explosion, reveal
const displayedCost = ref(0)

// Generate particles with random properties
const explosionParticles = ref([])

const generateParticles = () => {
  const arr = []
  for (let i = 0; i < 100; i++) {
    const angle = Math.random() * 360
    const distance = 50 + Math.random() * 400
    const size = 2 + Math.random() * 6
    const colors = ['#FFD700', '#FF6B6B', '#4ECDC4', '#A855F7', '#ffffff']
    arr.push({
      id: i,
      angle,
      distance,
      size,
      color: colors[Math.floor(Math.random() * colors.length)],
      delay: Math.random() * 0.3
    })
  }
  explosionParticles.value = arr
}

const startRevealAnimation = () => {
  isRevealing.value = true
  showResult.value = false
  animationPhase.value = 'buildup'
  generateParticles()
  
  // Phase 2: Energy (Starts at 1.5s)
  setTimeout(() => {
    animationPhase.value = 'energy'
  }, 1500)

  // Phase 3: Explosion (Starts at 3.2s)
  setTimeout(() => {
    animationPhase.value = 'explosion'
  }, 3200)

  // Phase 4: Reveal (Starts at 4.0s)
  setTimeout(() => {
    animationPhase.value = 'reveal'
    isRevealing.value = false
    showResult.value = true
    startCounting()
  }, 4000)
}

const startCounting = () => {
  const target = fiveYearCost.value
  const duration = 1500
  const startTime = performance.now()

  const tick = (currentTime) => {
    const elapsed = currentTime - startTime
    const progress = Math.min(elapsed / duration, 1)
    
    // Easing out quart
    const ease = 1 - Math.pow(1 - progress, 4)
    
    displayedCost.value = Math.floor(target * ease)

    if (progress < 1) {
      requestAnimationFrame(tick)
    }
  }
  requestAnimationFrame(tick)
}

const predictionPrize = computed(() => {
  const cost = fiveYearCost.value
  if (cost >= 3000) return { text: 'RIJBEWIJS', icon: '🚗', color: '#10B981', class: 'theme-green' } // Green
  if (cost >= 1500) return { text: 'LUXE VAKANTIE', icon: '🌴', color: '#F59E0B', class: 'theme-orange' } // Orange
  if (cost >= 800) return { text: 'FATBIKE', icon: '🚲', color: '#3B82F6', class: 'theme-blue' } // Blue
  return { text: 'EEN MOOI BEGIN', icon: '💰', color: '#D9F904', class: 'theme-default' }
})
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
      
      <p class="subtext-small">Als je zo doorgaat had je dit kunnen kopen</p>
      <br />

      <!-- Reveal Animation / Result -->
      <div class="prize-section" :class="{ 'showing-result': showResult }">
        
        <!-- Full Screen Animation Overlay -->
        <Teleport to="body">
          <div v-if="isRevealing" class="anim-overlay" :class="animationPhase">
            <!-- Phase 1 & 2: Orb -->
            <div class="orb-container" :class="{ 'exploding': animationPhase === 'explosion' }">
              <div class="orb-glow"></div>
              <div class="orb"></div>
              <div class="orb-core"></div>
              
              <!-- Floating particles around orb -->
              <div class="floating-particles">
                <span v-for="n in 20" :key="n" class="float-particle" :style="{ animationDelay: `${n * 0.1}s` }"></span>
              </div>
            </div>

            <!-- Lightning bolts (Phase 2) -->
            <div v-if="animationPhase === 'energy'" class="lightning-container">
              <svg class="lightning-bolt l1" viewBox="0 0 100 200">
                <path d="M50 0 L30 80 L55 80 L25 200 L70 90 L45 90 Z" fill="white"/>
              </svg>
              <svg class="lightning-bolt l2" viewBox="0 0 100 200">
                <path d="M50 0 L30 80 L55 80 L25 200 L70 90 L45 90 Z" fill="white"/>
              </svg>
              <svg class="lightning-bolt l3" viewBox="0 0 100 200">
                <path d="M50 0 L30 80 L55 80 L25 200 L70 90 L45 90 Z" fill="white"/>
              </svg>
              <svg class="lightning-bolt l4" viewBox="0 0 100 200">
                <path d="M50 0 L30 80 L55 80 L25 200 L70 90 L45 90 Z" fill="white"/>
              </svg>
            </div>

            <!-- Ripple effects (Phase 2) -->
            <div v-if="animationPhase === 'energy'" class="ripples">
              <div class="ripple r1"></div>
              <div class="ripple r2"></div>
              <div class="ripple r3"></div>
            </div>

            <!-- Explosion particles (Phase 3) -->
            <div v-if="animationPhase === 'explosion'" class="explosion-container">
              <div 
                v-for="p in explosionParticles" 
                :key="p.id" 
                class="explosion-particle"
                :style="{
                  '--angle': p.angle + 'deg',
                  '--distance': p.distance + 'px',
                  '--size': p.size + 'px',
                  '--color': p.color,
                  '--delay': p.delay + 's'
                }"
              ></div>
            </div>

            <!-- Shockwave (Phase 3) -->
            <div v-if="animationPhase === 'explosion'" class="shockwave"></div>
            
            <!-- Lens flare (Phase 3) -->
            <div v-if="animationPhase === 'explosion'" class="lens-flare"></div>

            <!-- Text overlay -->
            <p class="reveal-text" :class="animationPhase">
              <span v-if="animationPhase === 'buildup'">JE HAD OOK KUNNEN HEBBEN...</span>
              <span v-else-if="animationPhase === 'energy'">MAAK JE KLAAR!</span>
            </p>
          </div>
        </Teleport>

        <!-- Result Display -->
        <div v-if="showResult" class="prize-result-container">
          <div class="confetti-container">
            <div v-for="n in 50" :key="n" class="confetti-piece" :style="{ '--delay': `${Math.random() * 2}s`, '--x': `${Math.random() * 100}%`, '--color': ['#FFD700', '#FF6B6B', '#4ECDC4', '#A855F7'][Math.floor(Math.random() * 4)] }"></div>
          </div>
          
          <div class="result-3d-wrapper pop-in-bounce">
            <div class="card-bg" :style="{ backgroundColor: predictionPrize.color }"></div>
            <div class="goal-item big-goal">
              <span class="goal-icon main-icon">{{ predictionPrize.icon }}</span>
              <span class="goal-text main-prize">{{ predictionPrize.text }}</span>
            </div>
          </div>

          <div class="cost-counter fade-up">
            <span class="currency">€</span>
            <span class="amount">{{ displayedCost }}</span>
          </div>

          <p class="final-caption fade-up-delayed">Had je kunnen hebben!</p>
        </div>
      </div>

      <button v-if="showResult" class="finish-btn pop-in-delayed" @click="$emit('finish')">
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
  z-index: 100;
  position: relative;
  margin-top: 2rem;
}

/* ========================================
   FULLSCREEN ANIMATION OVERLAY
   ======================================== */
.anim-overlay {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  width: 100vw;
  height: 100vh;
  background-color: #000;
  z-index: 99999;
  display: flex;
  justify-content: center;
  align-items: center;
  overflow: hidden;
  animation: fadeToBlack 0.3s ease-out forwards;
}

@keyframes fadeToBlack {
  from { opacity: 0; }
  to { opacity: 1; }
}

/* ---- ORB CONTAINER ---- */
.orb-container {
  position: relative;
  width: 120px;
  height: 120px;
  display: flex;
  justify-content: center;
  align-items: center;
}

.orb-container.exploding {
  animation: orbExplode 0.3s ease-out forwards;
}

@keyframes orbExplode {
  0% { transform: scale(1); }
  50% { transform: scale(2); }
  100% { transform: scale(0); opacity: 0; }
}

.orb-glow {
  position: absolute;
  width: 200%;
  height: 200%;
  background: radial-gradient(circle, rgba(255, 215, 0, 0.4) 0%, transparent 70%);
  border-radius: 50%;
  animation: glowPulse 1s ease-in-out infinite;
}

.orb {
  position: absolute;
  width: 100%;
  height: 100%;
  background: radial-gradient(circle at 30% 30%, #fff 0%, #FFD700 40%, #FF8C00 100%);
  border-radius: 50%;
  box-shadow: 
    0 0 60px #FFD700,
    0 0 100px #FF8C00,
    inset 0 0 30px rgba(255,255,255,0.5);
}

.orb-core {
  position: absolute;
  width: 40%;
  height: 40%;
  background: white;
  border-radius: 50%;
  box-shadow: 0 0 40px white;
}

/* Floating particles around orb */
.floating-particles {
  position: absolute;
  width: 300%;
  height: 300%;
}

.float-particle {
  position: absolute;
  width: 6px;
  height: 6px;
  background: #FFD700;
  border-radius: 50%;
  animation: floatAround 2s ease-in-out infinite;
}

.float-particle:nth-child(odd) { background: white; }
.float-particle:nth-child(1) { top: 10%; left: 50%; }
.float-particle:nth-child(2) { top: 20%; left: 80%; }
.float-particle:nth-child(3) { top: 50%; left: 90%; }
.float-particle:nth-child(4) { top: 80%; left: 80%; }
.float-particle:nth-child(5) { top: 90%; left: 50%; }
.float-particle:nth-child(6) { top: 80%; left: 20%; }
.float-particle:nth-child(7) { top: 50%; left: 10%; }
.float-particle:nth-child(8) { top: 20%; left: 20%; }

@keyframes floatAround {
  0%, 100% { transform: translateY(0) scale(1); opacity: 0.8; }
  50% { transform: translateY(-10px) scale(1.2); opacity: 1; }
}

@keyframes glowPulse {
  0%, 100% { transform: scale(1); opacity: 0.5; }
  50% { transform: scale(1.3); opacity: 0.8; }
}

/* ---- PHASE: BUILDUP ---- */
.anim-overlay.buildup .orb {
  animation: orbPulse 0.8s ease-in-out infinite;
}

.anim-overlay.buildup .orb-core {
  animation: corePulse 0.8s ease-in-out infinite;
}

@keyframes orbPulse {
  0%, 100% { transform: scale(1); }
  50% { transform: scale(1.1); }
}

@keyframes corePulse {
  0%, 100% { transform: scale(1); opacity: 1; }
  50% { transform: scale(0.8); opacity: 0.8; }
}

/* ---- PHASE: ENERGY ---- */
.anim-overlay.energy {
  background: radial-gradient(circle at center, #1a1a2e 0%, #000 100%);
  animation: screenShake 0.08s infinite;
}

.anim-overlay.energy .orb {
  animation: orbIntense 0.15s ease-in-out infinite;
  box-shadow: 
    0 0 80px #FFD700,
    0 0 150px #FF8C00,
    0 0 200px #fff;
}

.anim-overlay.energy .orb-glow {
  animation: glowIntense 0.2s ease-in-out infinite;
  width: 400%;
  height: 400%;
}

@keyframes orbIntense {
  0%, 100% { transform: scale(1); }
  50% { transform: scale(1.3); }
}

@keyframes glowIntense {
  0%, 100% { opacity: 0.5; }
  50% { opacity: 1; }
}

@keyframes screenShake {
  0% { transform: translate(0, 0); }
  20% { transform: translate(-3px, 3px); }
  40% { transform: translate(3px, -3px); }
  60% { transform: translate(-3px, -3px); }
  80% { transform: translate(3px, 3px); }
  100% { transform: translate(0, 0); }
}

/* Lightning bolts */
.lightning-container {
  position: absolute;
  width: 100%;
  height: 100%;
  pointer-events: none;
}

.lightning-bolt {
  position: absolute;
  width: 60px;
  height: 150px;
  opacity: 0;
  filter: drop-shadow(0 0 10px white) drop-shadow(0 0 20px #00f);
  animation: lightningStrike 0.15s ease-out infinite;
}

.lightning-bolt.l1 { top: 0; left: 50%; transform: translateX(-50%) rotate(10deg); animation-delay: 0s; }
.lightning-bolt.l2 { top: 50%; right: 0; transform: rotate(90deg); animation-delay: 0.05s; }
.lightning-bolt.l3 { bottom: 0; left: 50%; transform: translateX(-50%) rotate(180deg); animation-delay: 0.1s; }
.lightning-bolt.l4 { top: 50%; left: 0; transform: rotate(-90deg); animation-delay: 0.15s; }

@keyframes lightningStrike {
  0%, 100% { opacity: 0; }
  10%, 30% { opacity: 1; }
  20%, 40% { opacity: 0.3; }
}

/* Ripple effects */
.ripples {
  position: absolute;
  width: 100%;
  height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
  pointer-events: none;
}

.ripple {
  position: absolute;
  border: 3px solid rgba(255, 215, 0, 0.6);
  border-radius: 50%;
  animation: rippleExpand 1s ease-out infinite;
}

.ripple.r1 { animation-delay: 0s; }
.ripple.r2 { animation-delay: 0.3s; }
.ripple.r3 { animation-delay: 0.6s; }

@keyframes rippleExpand {
  0% { width: 100px; height: 100px; opacity: 1; }
  100% { width: 600px; height: 600px; opacity: 0; }
}

/* ---- PHASE: EXPLOSION ---- */
.anim-overlay.explosion {
  background: white;
  animation: explosionFlash 0.8s ease-out forwards;
}

@keyframes explosionFlash {
  0% { background: white; }
  30% { background: #FFD700; }
  100% { background: transparent; opacity: 0; }
}

/* Explosion particles */
.explosion-container {
  position: absolute;
  width: 100%;
  height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
}

.explosion-particle {
  position: absolute;
  width: var(--size);
  height: var(--size);
  background: var(--color);
  border-radius: 50%;
  animation: particleExplode 0.8s ease-out forwards;
  animation-delay: var(--delay);
  box-shadow: 0 0 6px var(--color);
}

@keyframes particleExplode {
  0% {
    transform: translate(0, 0) scale(1);
    opacity: 1;
  }
  100% {
    transform: 
      translate(
        calc(cos(var(--angle)) * var(--distance)),
        calc(sin(var(--angle)) * var(--distance))
      ) 
      scale(0);
    opacity: 0;
  }
}

/* Shockwave */
.shockwave {
  position: absolute;
  width: 50px;
  height: 50px;
  border: 8px solid rgba(255, 255, 255, 0.8);
  border-radius: 50%;
  animation: shockwaveExpand 0.6s ease-out forwards;
}

@keyframes shockwaveExpand {
  0% { 
    width: 50px; 
    height: 50px; 
    opacity: 1;
    border-width: 8px;
  }
  100% { 
    width: 250vmax; 
    height: 250vmax; 
    opacity: 0;
    border-width: 2px;
  }
}

/* Lens flare */
.lens-flare {
  position: absolute;
  width: 100%;
  height: 100%;
  background: radial-gradient(circle at center, rgba(255,255,255,1) 0%, rgba(255,255,255,0) 50%);
  animation: flareFade 0.5s ease-out forwards;
}

@keyframes flareFade {
  0% { opacity: 1; transform: scale(0.5); }
  50% { opacity: 1; transform: scale(2); }
  100% { opacity: 0; transform: scale(3); }
}

/* Reveal text */
.reveal-text {
  position: absolute;
  bottom: 20%;
  font-family: 'Inter', sans-serif;
  font-weight: 900;
  font-style: italic;
  font-size: 1.5rem;
  color: white;
  text-transform: uppercase;
  text-shadow: 0 0 20px rgba(255,215,0,0.8);
  animation: textPulse 0.5s ease-in-out infinite;
}

.reveal-text.energy {
  animation: textShake 0.1s infinite, textPulse 0.3s ease-in-out infinite;
}

@keyframes textPulse {
  0%, 100% { opacity: 0.8; }
  50% { opacity: 1; }
}

@keyframes textShake {
  0%, 100% { transform: translateX(0); }
  25% { transform: translateX(-2px); }
  75% { transform: translateX(2px); }
}

/* ========================================
   RESULT DISPLAY
   ======================================== */
.prize-section {
  width: 100%;
  min-height: 300px;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  position: relative;
}

.prize-result-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  width: 100%;
  perspective: 1000px;
  position: relative;
}

/* Confetti */
.confetti-container {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  pointer-events: none;
  overflow: hidden;
  z-index: 50;
}

.confetti-piece {
  position: absolute;
  top: -20px;
  left: var(--x);
  width: 10px;
  height: 10px;
  background: var(--color);
  animation: confettiFall 3s ease-out forwards;
  animation-delay: var(--delay);
}

.confetti-piece:nth-child(odd) {
  width: 8px;
  height: 16px;
  border-radius: 0;
}

@keyframes confettiFall {
  0% {
    transform: translateY(0) rotate(0deg);
    opacity: 1;
  }
  100% {
    transform: translateY(100vh) rotate(720deg);
    opacity: 0;
  }
}

.result-3d-wrapper {
  position: relative;
  margin-bottom: 2rem;
  width: 100%;
  max-width: 350px;
  height: 150px;
  transform-style: preserve-3d;
  z-index: 10;
}

.result-3d-wrapper .card-bg {
  position: absolute;
  top: 8px;
  left: 8px;
  width: 100%;
  height: 100%;
  background-color: #D9F904;
  border: 2px solid black;
  z-index: 0;
}

.result-3d-wrapper .goal-item {
  position: relative;
  width: 100%;
  height: 100%;
  background-color: #E5E7EB;
  border: 2px solid black;
  z-index: 10;
  display: flex;
  justify-content: center;
  align-items: center;
  gap: 15px;
  flex-direction: row;
}

.main-prize {
  font-size: 1.8rem;
  font-weight: 900;
  font-style: italic;
  color: #1E3A8A;
  text-transform: uppercase;
}

.main-icon {
  font-size: 4rem;
}

.cost-counter {
  font-family: 'Inter', sans-serif;
  font-weight: 900;
  font-style: italic;
  font-size: 4rem;
  color: #1E3A8A;
  display: flex;
  gap: 5px;
  text-shadow: 2px 2px 0px rgba(0,0,0,0.1);
  z-index: 10;
}

.final-caption {
  font-weight: 700;
  font-style: italic;
  color: #1E3A8A;
  font-size: 1.5rem;
  margin-top: 1rem;
  z-index: 10;
}

/* Result Reveal Animations */
.pop-in-bounce {
  animation: popInBounce 0.8s cubic-bezier(0.175, 0.885, 0.32, 1.275) forwards;
}

@keyframes popInBounce {
  0% { 
    opacity: 0; 
    transform: scale(0) rotateY(180deg) translateY(50px); 
  }
  60% { 
    transform: scale(1.15) rotateY(0deg) translateY(-10px); 
  }
  80% { 
    transform: scale(0.95) rotateY(0deg) translateY(5px); 
  }
  100% { 
    opacity: 1; 
    transform: scale(1) rotateY(0deg) translateY(0); 
  }
}

.fade-up {
  opacity: 0;
  animation: fadeInUp 0.5s ease-out 0.5s forwards;
}

.fade-up-delayed {
  opacity: 0;
  animation: fadeInUp 0.5s ease-out 1s forwards;
}

@keyframes fadeInUp {
  from { opacity: 0; transform: translateY(30px); }
  to { opacity: 1; transform: translateY(0); }
}

.pop-in-delayed {
  opacity: 0;
  animation: popIn 0.5s cubic-bezier(0.175, 0.885, 0.32, 1.275) 1.5s forwards;
}

@keyframes popIn {
  0% { opacity: 0; transform: scale(0.5); }
  100% { opacity: 1; transform: scale(1); }
}
</style>
