<template>
  <div class="wrapped-overlay">
    <div class="progress-bar">
      <div 
        v-for="(s, i) in totalSlides" 
        :key="i" 
        class="progress-segment"
        @click.stop="goToSlide(i)"
      >
        <div 
          class="progress-fill" 
          :class="{ active: i === currentSlide, visited: i < currentSlide }"
        ></div>
      </div>
    </div>

    <div class="slide-container" @click="nextSlide">
      <transition name="slide-fade" mode="out-in">
        
        <!-- Slide 1: Costs -->
        <div v-if="activeSlideId === 'costs'" class="slide slide-costs" key="costs">
          <div class="content-wrapper">
            <h2 class="slide-title animate-in delay-1">Jouw Vape Jaar</h2>
            <div class="big-stat animate-in delay-2">
              <span class="currency">€</span>
              <span class="amount">{{ fmt(yearlyCost) }}</span>
            </div>
            <p class="subtitle animate-in delay-3">geef je per jaar uit aan vapen.</p>
            <div class="sub-stats animate-in delay-4">
              <div class="stat-item glass">
                <strong>{{ fmt(monthlyCost) }}</strong>
                <span>per maand</span>
              </div>
              <div class="stat-item glass">
                <strong>{{ fmt(weeklyCost) }}</strong>
                <span>per week</span>
              </div>
            </div>
          </div>
        </div>

        <!-- Slide 2: Comparisons -->
        <div v-else-if="activeSlideId === 'comparisons'" class="slide slide-comparisons" key="comparisons">
          <div class="content-wrapper">
            <h2 class="slide-title animate-in delay-1">Wat had je kunnen kopen?</h2>
            <div class="comparison-grid">
              <div 
                v-for="(item, idx) in comparisons" 
                :key="item.id" 
                class="comp-item glass animate-in"
                :style="{ animationDelay: `${(idx * 0.1) + 0.3}s` }"
              >
                <div class="comp-icon">{{ item.icon }}</div>
                <div class="comp-details">
                  <div class="comp-count">{{ eqCount(yearlyCost, item.cost) }}</div>
                  <div class="comp-name">{{ item.name }}</div>
                </div>
              </div>
            </div>
          </div>
        </div>

        <!-- Slide 3: Time -->
        <div v-else-if="activeSlideId === 'time'" class="slide slide-time" key="time">
          <div class="content-wrapper">
            <h2 class="slide-title animate-in delay-1">Tijd Verspild</h2>
            <div class="big-stat animate-in delay-2">
              <span class="amount">{{ hoursWasted }}</span>
              <span class="unit">uur</span>
            </div>
            <p class="subtitle animate-in delay-3">heb je dit jaar besteed aan vapen.</p>
            <div class="context-box glass animate-in delay-4">
              <p>Dat zijn <strong>{{ Math.floor(hoursWasted / 0.6) }}</strong> afleveringen van je favoriete serie.</p>
              <div class="divider"></div>
              <p>Of <strong>{{ Math.floor(hoursWasted * 60 / 3.5) }}</strong> keer je favoriete nummer luisteren.</p>
            </div>
          </div>
        </div>

        <!-- Slide 4: Health -->
        <div v-else-if="activeSlideId === 'health'" class="slide slide-health" key="health">
          <div class="content-wrapper">
            <h2 class="slide-title animate-in delay-1">De Gezondheidsrealiteit</h2>
            <div class="big-stat warning animate-in delay-2">
              <span class="amount">{{ totalPuffs.toLocaleString() }}</span>
            </div>
            <p class="subtitle animate-in delay-3">inhalaties per jaar.</p>
            <div class="health-visual animate-in delay-4">
              <div class="lung-icon pulse">🫁</div>
              <p>Je longen verwerken dit allemaal.</p>
            </div>
          </div>
        </div>

        <!-- Slide 5: Future -->
        <div v-else-if="activeSlideId === 'future'" class="slide slide-future" key="future">
          <div class="content-wrapper">
            <h2 class="slide-title animate-in delay-1">Jouw 5-Jaar Voorspelling</h2>
            <p class="subtitle animate-in delay-2">Als je zo doorgaat...</p>
            <div class="big-stat danger animate-in delay-3">
              <span class="currency">€</span>
              <span class="amount">{{ fmt(yearlyCost * 5) }}</span>
            </div>
            <p class="subtitle animate-in delay-4">in rook opgegaan.</p>
            <div class="future-list animate-in delay-5">
              <div class="future-item glass" v-if="yearlyCost * 5 > 2000">✈️ Een luxe vakantie</div>
              <div class="future-item glass" v-if="yearlyCost * 5 > 1500">🛵 Een scooter</div>
              <div class="future-item glass" v-if="yearlyCost * 5 > 2500">🚗 Rijbewijs + eerste auto</div>
              <div class="future-item glass" v-if="yearlyCost * 5 > 1000">💻 High-end Laptop</div>
            </div>
          </div>
        </div>

        <!-- Slide 6: Stats -->
        <div v-else-if="activeSlideId === 'stats'" class="slide slide-stats" key="stats">
          <div class="content-wrapper">
            <h2 class="slide-title animate-in delay-1">Je Bent Niet Alleen</h2>
            <div class="stat-card glass animate-in delay-2">
              <h3>{{ age ? `Mensen van ${age} jaar` : 'Jouw leeftijdsgroep' }}</h3>
              <p>Gemiddeld geven jongeren zo'n <strong>€800</strong> per jaar uit.</p>
              <div class="comparison-bar-container">
                <div class="comparison-bar">
                  <div class="bar-fill" :style="{ width: Math.min(100, (yearlyCost / 1500) * 100) + '%' }"></div>
                </div>
                <div class="bar-labels">
                  <span>Gemiddeld</span>
                  <span>Jij (€{{ yearlyCost }})</span>
                </div>
              </div>
            </div>
            <button class="btn primary animate-in delay-3" @click="nextSlide">
              Volgende
            </button>
          </div>
        </div>

        <!-- Slide 7: Savings (Interactive) -->
        <div v-else-if="activeSlideId === 'savings'" class="slide slide-savings" key="savings" @click.stop>
          <div class="content-wrapper">
            <h2 class="slide-title animate-in delay-1">Wat Als?</h2>
            <p class="subtitle animate-in delay-2">Als je {{ reduction }}% minder zou vapen...</p>
            
            <div class="slider-container animate-in delay-3">
              <input type="range" min="0" max="100" step="10" v-model="reduction" class="slider" />
              <div class="slider-value">{{ reduction }}%</div>
            </div>
            
            <div class="savings-result animate-in delay-4">
              <p>Bespaar je</p>
              <div class="big-stat success">
                <span class="currency">€</span>
                <span class="amount">{{ fmt(yearlyCost * (reduction / 100)) }}</span>
              </div>
              <p>per jaar!</p>
            </div>
            <button class="btn primary animate-in delay-5" @click="nextSlide">Volgende</button>
          </div>
        </div>

        <!-- Slide 8: Positive Spin -->
        <div v-else-if="activeSlideId === 'positive'" class="slide slide-positive" key="positive">
          <div class="content-wrapper">
            <h2 class="slide-title animate-in delay-1">Het Goede Nieuws 🌟</h2>
            <p class="subtitle animate-in delay-2">Je lichaam herstelt razendsnel!</p>
            
            <div class="recovery-timeline animate-in delay-3">
              <div class="recovery-item glass">
                <div class="time">20 min</div>
                <div class="benefit">Hartslag wordt weer normaal ❤️</div>
              </div>
              <div class="recovery-item glass">
                <div class="time">48 uur</div>
                <div class="benefit">Je proeft en ruikt weer beter 🍕</div>
              </div>
              <div class="recovery-item glass">
                <div class="time">2 weken</div>
                <div class="benefit">Conditie en longfunctie verbeteren 🏃‍♂️</div>
              </div>
              <div class="recovery-item glass">
                <div class="time">1 jaar</div>
                <div class="benefit">Risico op hartziekten gehalveerd 📉</div>
              </div>
            </div>
            <button class="btn primary animate-in delay-4" @click="nextSlide">Volgende</button>
          </div>
        </div>

        <!-- Slide 9: Action -->
        <div v-else-if="activeSlideId === 'action'" class="slide slide-action" key="action">
          <div class="content-wrapper">
            <h2 class="slide-title animate-in delay-1">Jouw Actieplan</h2>
            <div class="action-buttons animate-in delay-2">
              <button class="btn primary big pulse-hover" @click="$emit('close')">Opnieuw berekenen</button>
              <button class="btn ghost big" @click="share">Deel resultaat 📸</button>
            </div>
            <div class="tips glass animate-in delay-3">
              <h3>Tips om te minderen:</h3>
              <ul>
                <li>📉 Stel een dagelijks limiet in</li>
                <li>💰 Bereken je besparing per week</li>
                <li>🏃‍♂️ Zoek afleiding als je trek krijgt</li>
              </ul>
            </div>
          </div>
        </div>

      </transition>
    </div>
    
    <div class="nav-hint">Klik om verder te gaan</div>
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
  yearsVaping: [Number, String],
  slideSettings: {
    type: Object,
    default: () => ({
      costs: true,
      comparisons: true,
      time: true,
      health: true,
      future: true,
      stats: true,
      savings: true,
      positive: true,
      action: true
    })
  }
})

const emit = defineEmits(['close'])

const currentSlide = ref(0)
const reduction = ref(50)

const allSlides = [
  'costs', 'comparisons', 'time', 'health', 'future', 'stats', 'savings', 'positive', 'action'
]

const enabledSlides = computed(() => {
  return allSlides.filter(id => props.slideSettings[id])
})

const totalSlides = computed(() => enabledSlides.value.length)
const activeSlideId = computed(() => enabledSlides.value[currentSlide.value])

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
  if (currentSlide.value < totalSlides.value - 1) currentSlide.value++
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
  inset: 0;
  background: rgba(15, 23, 42, 0.95);
  backdrop-filter: blur(20px);
  z-index: 100;
  display: flex;
  flex-direction: column;
  color: white;
}

.progress-bar {
  display: flex;
  gap: 8px;
  padding: 20px;
  width: 100%;
  max-width: 600px;
  margin: 0 auto;
  z-index: 10;
}

.progress-segment {
  flex: 1;
  height: 4px;
  background: rgba(255, 255, 255, 0.2);
  border-radius: 2px;
  cursor: pointer;
  overflow: hidden;
}

.progress-fill {
  height: 100%;
  width: 0%;
  background: white;
  transition: width 0.3s ease;
}

.progress-fill.visited { width: 100%; }
.progress-fill.active { 
  width: 100%; 
  animation: progress 5s linear forwards; /* Auto advance visual cue */
}

.slide-container {
  flex: 1;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  position: relative;
  overflow: hidden;
}

.slide {
  width: 100%;
  max-width: 600px;
  padding: 20px;
  text-align: center;
}

.content-wrapper {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 24px;
}

.slide-title {
  font-size: 2rem;
  background: linear-gradient(to right, #fff, #94a3b8);
  -webkit-background-clip: text;
  background-clip: text;
  -webkit-text-fill-color: transparent;
  margin-bottom: 1rem;
}

.big-stat {
  font-size: 5rem;
  font-weight: 900;
  line-height: 1;
  color: var(--accent);
  text-shadow: 0 0 40px rgba(45, 212, 191, 0.3);
}

.big-stat.warning { color: #fbbf24; text-shadow: 0 0 40px rgba(251, 191, 36, 0.3); }
.big-stat.danger { color: #f87171; text-shadow: 0 0 40px rgba(248, 113, 113, 0.3); }

.currency { font-size: 0.5em; vertical-align: super; opacity: 0.8; }
.unit { font-size: 0.3em; margin-left: 10px; opacity: 0.8; }

.subtitle {
  font-size: 1.5rem;
  color: var(--text-muted);
  font-weight: 300;
}

.sub-stats {
  display: flex;
  gap: 20px;
  margin-top: 20px;
  width: 100%;
}

.stat-item {
  flex: 1;
  padding: 20px;
  border-radius: 16px;
  display: flex;
  flex-direction: column;
  gap: 8px;
}

.stat-item strong { font-size: 1.5rem; color: white; }
.stat-item span { font-size: 0.9rem; color: var(--text-muted); }

/* Comparisons Grid */
.comparison-grid {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 16px;
  width: 100%;
}

.comp-item {
  display: flex;
  align-items: center;
  gap: 16px;
  padding: 16px;
  border-radius: 16px;
  text-align: left;
  transition: transform 0.2s;
}

.comp-item:hover { transform: scale(1.02); background: rgba(255,255,255,0.1); }

.comp-icon { font-size: 2rem; }
.comp-count { font-size: 1.5rem; font-weight: 800; color: var(--accent); }
.comp-name { font-size: 0.9rem; color: var(--text-muted); line-height: 1.2; }

/* Context Box */
.context-box {
  padding: 24px;
  border-radius: 16px;
  width: 100%;
  text-align: left;
}
.divider { height: 1px; background: rgba(255,255,255,0.1); margin: 16px 0; }

/* Health */
.health-visual { margin-top: 40px; }
.lung-icon { font-size: 6rem; margin-bottom: 16px; }
.pulse { animation: pulse-glow 2s infinite; }

/* Future List */
.future-list { width: 100%; display: flex; flex-direction: column; gap: 12px; }
.future-item { padding: 16px; border-radius: 12px; font-weight: 600; }

/* Stats */
.stat-card { width: 100%; padding: 32px; border-radius: 24px; margin-bottom: 32px; }
.comparison-bar-container { margin-top: 24px; }
.comparison-bar { height: 12px; background: rgba(255,255,255,0.1); border-radius: 6px; overflow: hidden; margin-bottom: 8px; }
.bar-fill { height: 100%; background: var(--accent); border-radius: 6px; }
.bar-labels { display: flex; justify-content: space-between; font-size: 0.8rem; color: var(--text-muted); }

.nav-hint {
  text-align: center;
  padding: 20px;
  opacity: 0.5;
  font-size: 0.9rem;
  animation: float 2s infinite;
}

/* Animations */
.animate-in {
  opacity: 0;
  transform: translateY(20px);
  animation: slide-up 0.6s cubic-bezier(0.16, 1, 0.3, 1) forwards;
}

.delay-1 { animation-delay: 0.1s; }
.delay-2 { animation-delay: 0.2s; }
.delay-3 { animation-delay: 0.3s; }
.delay-4 { animation-delay: 0.4s; }
.delay-5 { animation-delay: 0.5s; }

/* Savings Slide */
.slider-container {
  width: 100%;
  max-width: 400px;
  margin: 20px 0;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 16px;
}

.slider {
  width: 100%;
  height: 6px;
  -webkit-appearance: none;
  appearance: none;
  background: rgba(255, 255, 255, 0.2);
  border-radius: 3px;
  outline: none;
}

.slider::-webkit-slider-thumb {
  -webkit-appearance: none;
  appearance: none;
  width: 24px;
  height: 24px;
  border-radius: 50%;
  background: var(--accent);
  cursor: pointer;
  box-shadow: 0 0 10px rgba(45, 212, 191, 0.5);
  transition: transform 0.2s;
}

.slider::-webkit-slider-thumb:hover {
  transform: scale(1.2);
}

.slider-value {
  font-size: 1.5rem;
  font-weight: 700;
  color: var(--accent);
}

.savings-result {
  margin: 32px 0;
  padding: 24px;
  background: rgba(255, 255, 255, 0.05);
  border-radius: 24px;
  border: 1px solid rgba(255, 255, 255, 0.1);
}

.big-stat.success {
  color: #34d399;
  text-shadow: 0 0 40px rgba(52, 211, 153, 0.3);
}

/* Action Slide */
.action-buttons {
  display: flex;
  flex-direction: column;
  gap: 16px;
  width: 100%;
  max-width: 300px;
}

.btn.big {
  padding: 20px;
  font-size: 1.1rem;
}

.btn.ghost {
  background: rgba(255, 255, 255, 0.1);
  border: 1px solid rgba(255, 255, 255, 0.2);
}

.btn.ghost:hover {
  background: rgba(255, 255, 255, 0.2);
}

.tips {
  margin-top: 32px;
  padding: 24px;
  border-radius: 24px;
  text-align: left;
  width: 100%;
}

.tips h3 {
  margin: 0 0 16px 0;
  color: var(--accent);
}

.tips ul {
  margin: 0;
  padding-left: 20px;
  color: var(--text-muted);
}

.tips li {
  margin-bottom: 12px;
  line-height: 1.5;
}

.pulse-hover:hover {
  animation: pulse-glow 1.5s infinite;
}

/* Recovery Slide */
.recovery-timeline {
  display: flex;
  flex-direction: column;
  gap: 16px;
  width: 100%;
  margin-top: 24px;
}

.recovery-item {
  display: flex;
  align-items: center;
  gap: 16px;
  padding: 16px;
  border-radius: 16px;
  text-align: left;
}

.recovery-item .time {
  font-weight: 800;
  color: var(--accent);
  min-width: 70px;
  font-size: 1.1rem;
}

.recovery-item .benefit {
  color: var(--text);
  font-size: 0.95rem;
}

/* Slide Transitions */
.slide-fade-enter-active,
.slide-fade-leave-active {
  transition: all 0.5s cubic-bezier(0.16, 1, 0.3, 1);
}

.slide-fade-enter-from {
  opacity: 0;
  transform: translateX(50px) scale(0.95);
}

.slide-fade-leave-to {
  opacity: 0;
  transform: translateX(-50px) scale(0.95);
}

@media (max-width: 600px) {
  .big-stat { font-size: 3.5rem; }
  .slide-title { font-size: 1.5rem; }
  .subtitle { font-size: 1.1rem; }
  .lung-icon { font-size: 4rem; }
  
  .comparison-grid {
    grid-template-columns: 1fr;
  }
  
  .sub-stats {
    flex-direction: column;
    gap: 12px;
  }
  
  .stat-item {
    padding: 16px;
  }
  
  .comp-item {
    padding: 12px;
  }
}
</style>
