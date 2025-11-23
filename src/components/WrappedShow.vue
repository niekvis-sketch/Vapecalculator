<template>
  <div class="wrapped-overlay">
    <div class="progress-bar">
      <div 
        v-for="(s, i) in totalSlides" 
        :key="i" 
        class="progress-dot" 
        :class="{ active: i === currentSlide, visited: i < currentSlide }"
        @click="goToSlide(i)"
      ></div>
    </div>

    <div class="slide-container" @click="nextSlide">
      <transition name="fade" mode="out-in">
        
        <!-- Slide 1: Costs -->
        <div v-if="currentSlide === 0" class="slide slide-costs" key="0">
          <h2 class="slide-title">Jouw Vape Jaar</h2>
          <div class="big-stat">
            <span class="currency">€</span>
            <span class="amount">{{ fmt(yearlyCost) }}</span>
          </div>
          <p class="subtitle">geef je per jaar uit aan vapen.</p>
          <div class="sub-stats">
            <div class="stat-item">
              <strong>{{ fmt(monthlyCost) }}</strong>
              <span>per maand</span>
            </div>
            <div class="stat-item">
              <strong>{{ fmt(weeklyCost) }}</strong>
              <span>per week</span>
            </div>
          </div>
        </div>

        <!-- Slide 2: Comparisons -->
        <div v-else-if="currentSlide === 1" class="slide slide-comparisons" key="1">
          <h2 class="slide-title">Wat had je kunnen kopen?</h2>
          <div class="comparison-grid">
            <div v-for="item in comparisons" :key="item.id" class="comp-item">
              <div class="comp-icon">{{ item.icon }}</div>
              <div class="comp-count">{{ eqCount(yearlyCost, item.cost) }}</div>
              <div class="comp-name">{{ item.name }}</div>
            </div>
          </div>
        </div>

        <!-- Slide 3: Time -->
        <div v-else-if="currentSlide === 2" class="slide slide-time" key="2">
          <h2 class="slide-title">Tijd Verspild</h2>
          <div class="big-stat">
            <span class="amount">{{ hoursWasted }}</span>
            <span class="unit">uur</span>
          </div>
          <p class="subtitle">heb je dit jaar besteed aan vapen.</p>
          <div class="context-box">
            <p>Dat zijn <strong>{{ Math.floor(hoursWasted / 0.6) }}</strong> afleveringen van je favoriete serie (40 min).</p>
            <p>Of <strong>{{ Math.floor(hoursWasted * 60 / 3.5) }}</strong> keer je favoriete nummer luisteren.</p>
          </div>
        </div>

        <!-- Slide 4: Health -->
        <div v-else-if="currentSlide === 3" class="slide slide-health" key="3">
          <h2 class="slide-title">De Gezondheidsrealiteit</h2>
          <div class="big-stat warning">
            <span class="amount">{{ totalPuffs.toLocaleString() }}</span>
          </div>
          <p class="subtitle">inhalaties per jaar.</p>
          <div class="health-visual">
            <div class="lung-icon">🫁</div>
            <p>Je longen verwerken dit allemaal.</p>
          </div>
        </div>

        <!-- Slide 5: Future -->
        <div v-else-if="currentSlide === 4" class="slide slide-future" key="4">
          <h2 class="slide-title">Jouw 5-Jaar Voorspelling</h2>
          <p class="subtitle">Als je zo doorgaat...</p>
          <div class="big-stat danger">
            <span class="currency">€</span>
            <span class="amount">{{ fmt(yearlyCost * 5) }}</span>
          </div>
          <p class="subtitle">in rook opgegaan.</p>
          <div class="future-list">
            <div class="future-item" v-if="yearlyCost * 5 > 2000">✈️ Een luxe vakantie</div>
            <div class="future-item" v-if="yearlyCost * 5 > 1500">🛵 Een scooter</div>
            <div class="future-item" v-if="yearlyCost * 5 > 2500">🚗 Rijbewijs + eerste auto</div>
            <div class="future-item" v-if="yearlyCost * 5 > 1000">💻 High-end Laptop</div>
          </div>
        </div>

        <!-- Slide 6: Stats -->
        <div v-else-if="currentSlide === 5" class="slide slide-stats" key="5">
          <h2 class="slide-title">Je Bent Niet Alleen</h2>
          <div class="stat-card">
            <h3>{{ age ? `Mensen van ${age} jaar` : 'Jouw leeftijdsgroep' }}</h3>
            <p>Gemiddeld geven jongeren zo'n <strong>€800</strong> per jaar uit.</p>
            <div class="comparison-bar">
              <div class="bar-fill" :style="{ width: Math.min(100, (yearlyCost / 1500) * 100) + '%' }"></div>
            </div>
            <p class="small-text" v-if="yearlyCost > 800">Jij zit daar boven.</p>
            <p class="small-text" v-else>Jij zit daar onder. Goed bezig!</p>
          </div>
        </div>

        <!-- Slide 7: Savings (Interactive) -->
        <div v-else-if="currentSlide === 6" class="slide slide-savings" key="6" @click.stop>
          <h2 class="slide-title">Wat Als?</h2>
          <p class="subtitle">Als je {{ reduction }}% minder zou vapen...</p>
          
          <input type="range" min="0" max="100" step="10" v-model="reduction" class="slider" />
          
          <div class="savings-result">
            <p>Bespaar je</p>
            <div class="big-stat success">
              <span class="currency">€</span>
              <span class="amount">{{ fmt(yearlyCost * (reduction / 100)) }}</span>
            </div>
            <p>per jaar!</p>
          </div>
          <button class="btn primary" @click="nextSlide">Volgende</button>
        </div>

        <!-- Slide 8: Action -->
        <div v-else-if="currentSlide === 7" class="slide slide-action" key="7">
          <h2 class="slide-title">Jouw Actieplan</h2>
          <div class="action-buttons">
            <button class="btn primary big" @click="$emit('close')">Opnieuw berekenen</button>
            <button class="btn ghost big" @click="share">Deel resultaat 📸</button>
          </div>
          <div class="tips">
            <h3>Tips om te minderen:</h3>
            <ul>
              <li>Stel een dagelijks limiet in</li>
              <li>Bereken je besparing per week</li>
              <li>Zoek afleiding als je trek krijgt</li>
            </ul>
          </div>
        </div>

      </transition>
    </div>

    <div class="nav-controls">
      <button class="nav-btn" @click.stop="prevSlide" :disabled="currentSlide === 0">←</button>
      <span class="slide-indicator">{{ currentSlide + 1 }} / {{ totalSlides }}</span>
      <button class="nav-btn" @click.stop="nextSlide" :disabled="currentSlide === totalSlides - 1">→</button>
    </div>
  </div>
</template>

<script setup>
import { computed, ref } from 'vue'

const props = defineProps({
  weeklyVapes: Number,
  costPerVape: Number,
  weeklyCost: Number,
  monthlyCost: Number,
  yearlyCost: Number,
  comparisons: Array,
  age: [Number, String],
  yearsVaping: [Number, String]
})

const emit = defineEmits(['close'])

const currentSlide = ref(0)
const totalSlides = 8
const reduction = ref(50)

// Calculations
const minutesPerVape = 10 // Assumption: 10 mins of active puffing per vape device? Or per session? 
// If weeklyVapes is "devices", and a device lasts maybe 1 day of heavy use or 3 days light use.
// Let's assume 1 disposable vape = 600 puffs. 
// 1 puff = 3 seconds? 600 * 3 = 1800 seconds = 30 mins of continuous inhaling? 
// Or maybe "time holding the device". 
// Let's go with the prompt's example: "bijv. 5 minuten per vape". 
// If input is "vapes per week" (devices), 5 mins seems low for a whole device.
// Maybe the user meant "sessions per week"? But the input says "Aantal vapes per week" which usually implies devices for disposables.
// Let's assume 1 device = 2 hours of "vaping time" (handling, puffing, etc) spread out?
// Actually, let's stick to a simpler metric: 1 device = 600 puffs.
// Time wasted: 600 puffs * 5 seconds (puff + pause) = 3000 seconds = 50 mins per device?
// Let's be conservative: 1 hour per device.
const hoursWasted = computed(() => Math.round(props.weeklyVapes * 1 * 52)) 

const totalPuffs = computed(() => Math.round(props.weeklyVapes * 600 * 52)) // 600 puffs per vape

function nextSlide() {
  if (currentSlide.value < totalSlides - 1) currentSlide.value++
}

function prevSlide() {
  if (currentSlide.value > 0) currentSlide.value--
}

function goToSlide(i) {
  currentSlide.value = i
}

const formatter = new Intl.NumberFormat('nl-NL', { style: 'currency', currency: 'EUR', maximumFractionDigits: 0 })
function fmt(v){ return formatter.format(v) }
function eqCount(amount, itemCost){ if (!itemCost) return 0; return Math.max(0, Math.floor(amount / itemCost)) }

function share() {
  alert("Maak een screenshot om te delen! (Feature coming soon)")
}
</script>

<style scoped>
.wrapped-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: linear-gradient(135deg, #1e1e2e 0%, #2d2b55 100%);
  color: white;
  z-index: 100;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  overflow: hidden;
}

.progress-bar {
  position: absolute;
  top: 20px;
  display: flex;
  gap: 8px;
  z-index: 10;
}

.progress-dot {
  width: 8px;
  height: 8px;
  background: rgba(255,255,255,0.3);
  border-radius: 50%;
  cursor: pointer;
  transition: all 0.3s;
}

.progress-dot.active { background: #fff; transform: scale(1.2); }
.progress-dot.visited { background: rgba(255,255,255,0.7); }

.slide-container {
  width: 100%;
  max-width: 600px;
  height: 80%;
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 20px;
  cursor: pointer;
}

.slide {
  text-align: center;
  width: 100%;
  animation: fadeIn 0.5s ease-out;
}

.slide-title {
  font-size: 1.8rem;
  margin-bottom: 20px;
  font-weight: 700;
  text-transform: uppercase;
  letter-spacing: 1px;
  color: #a5b4fc;
}

.big-stat {
  font-size: 4rem;
  font-weight: 900;
  margin: 20px 0;
  color: #fff;
  text-shadow: 0 4px 12px rgba(0,0,0,0.3);
}

.big-stat.warning { color: #fbbf24; }
.big-stat.danger { color: #f87171; }
.big-stat.success { color: #34d399; }

.currency { font-size: 2rem; vertical-align: super; opacity: 0.8; }
.unit { font-size: 1.5rem; margin-left: 10px; opacity: 0.8; }

.subtitle { font-size: 1.2rem; opacity: 0.9; margin-bottom: 30px; }

.sub-stats { display: flex; justify-content: center; gap: 30px; margin-top: 40px; }
.stat-item { display: flex; flex-direction: column; }
.stat-item strong { font-size: 1.5rem; }
.stat-item span { font-size: 0.9rem; opacity: 0.7; }

.comparison-grid {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 15px;
}

.comp-item {
  background: rgba(255,255,255,0.1);
  padding: 15px;
  border-radius: 12px;
  display: flex;
  flex-direction: column;
  align-items: center;
}

.comp-icon { font-size: 2rem; margin-bottom: 5px; }
.comp-count { font-size: 1.5rem; font-weight: bold; }
.comp-name { font-size: 0.9rem; opacity: 0.8; }

.context-box { background: rgba(255,255,255,0.1); padding: 20px; border-radius: 12px; margin-top: 20px; }
.context-box p { margin: 10px 0; }

.future-list { display: flex; flex-direction: column; gap: 10px; align-items: center; }
.future-item { background: rgba(255,255,255,0.1); padding: 10px 20px; border-radius: 20px; width: 100%; max-width: 300px; }

.slider { width: 80%; margin: 30px 0; accent-color: #34d399; }

.nav-controls {
  position: absolute;
  bottom: 30px;
  display: flex;
  align-items: center;
  gap: 20px;
}

.nav-btn {
  background: rgba(255,255,255,0.2);
  border: none;
  color: white;
  width: 40px;
  height: 40px;
  border-radius: 50%;
  cursor: pointer;
  font-size: 1.2rem;
}
.nav-btn:disabled { opacity: 0.3; cursor: not-allowed; }

.btn {
  padding: 12px 24px;
  border-radius: 30px;
  border: none;
  font-weight: bold;
  cursor: pointer;
  margin: 10px;
  transition: transform 0.2s;
}
.btn:hover { transform: scale(1.05); }
.btn.primary { background: #4f8ef7; color: white; }
.btn.ghost { background: rgba(255,255,255,0.2); color: white; }

.tips { text-align: left; margin-top: 30px; background: rgba(0,0,0,0.2); padding: 20px; border-radius: 12px; }
.tips ul { padding-left: 20px; }
.tips li { margin-bottom: 8px; }

/* Transitions */
.fade-enter-active, .fade-leave-active { transition: opacity 0.3s ease; }
.fade-enter-from, .fade-leave-to { opacity: 0; }

@keyframes fadeIn {
  from { opacity: 0; transform: translateY(10px); }
  to { opacity: 1; transform: translateY(0); }
}
</style>
