<script setup>
import { toRefs } from 'vue'
const props = defineProps({
  weeklyVapes: { type: Number, required: true },
  costPerVape: { type: Number, required: true },
  age: { type: [Number, String], default: '' },
  yearsVaping: { type: [Number, String], default: '' }
})
const emit = defineEmits(['update:weeklyVapes', 'update:costPerVape', 'update:age', 'update:yearsVaping', 'start'])

function preventInvalid(e) {
  if (['-', '+', 'e', 'E'].includes(e.key)) e.preventDefault()
}

function clamp(val, isFloat = false) {
  if (val === '') return ''
  let n = isFloat ? parseFloat(val) : parseInt(val)
  if (isNaN(n)) return ''
  if (n < 0) n = 0
  if (n > 999) n = 999
  return n
}

function setWeekly(e) { 
  let v = e.target.value
  if (v.length > 3) {
    v = v.slice(0, 3)
    e.target.value = v
  }
  let n = clamp(v)
  if (n !== '') emit('update:weeklyVapes', n)
}

function setCost(e) {
  let v = e.target.value
  if (v === '') return emit('update:costPerVape', 0)
  
  // Check integer part length
  let parts = v.split('.')
  if (parts[0].length > 3) {
    parts[0] = parts[0].slice(0, 3)
    v = parts.join('.')
    e.target.value = v
  }

  let n = parseFloat(v)
  if (isNaN(n)) return
  
  if (n < 0) { n = 0; e.target.value = n; }
  if (n > 999) { n = 999; e.target.value = n; }
  
  if (n === props.costPerVape && String(v).includes('.')) return
  
  emit('update:costPerVape', n)
}

function setAge(e) { 
  let v = e.target.value
  if (v.length > 3) {
    v = v.slice(0, 3)
    e.target.value = v
  }
  emit('update:age', clamp(v)) 
}

function setYears(e) {
  let v = e.target.value
  if (v === '') return emit('update:yearsVaping', '')
  
  let parts = v.split('.')
  if (parts[0].length > 3) {
    parts[0] = parts[0].slice(0, 3)
    v = parts.join('.')
    e.target.value = v
  }

  let n = parseFloat(v)
  if (isNaN(n)) return
  if (n < 0) { n = 0; e.target.value = n; }
  if (n > 999) { n = 999; e.target.value = n; }
  
  if (n === props.yearsVaping && String(v).includes('.')) return
  emit('update:yearsVaping', n)
}
</script>

<template>
  <section class="input-panel">
    <div class="welcome-text">
      <h2>Wat kost jouw vape?</h2>
      <p>Vul je gegevens in om de berekening te maken.</p>
    </div>
    <form class="fields" @submit.prevent>
      <div class="field-group">
        <div class="field">
          <label for="weekly">Aantal vapes per week</label>
          <div class="input-wrapper">
            <input 
              id="weekly" 
              placeholder="Bijv. 2" 
              type="number" 
              min="0" 
              max="999"
              @keydown="preventInvalid"
              :value="weeklyVapes" 
              @input="setWeekly($event)" 
            />
            <div class="input-highlight"></div>
          </div>
        </div>

        <div class="presets">
          <button class="chip" :class="{ active: weeklyVapes === 1 }" @click.prevent="setWeekly({ target: { value: 1 } })">1/wk</button>
          <button class="chip" :class="{ active: weeklyVapes === 2 }" @click.prevent="setWeekly({ target: { value: 2 } })">2/wk</button>
          <button class="chip" :class="{ active: weeklyVapes === 3 }" @click.prevent="setWeekly({ target: { value: 3 } })">3/wk</button>
        </div>
      </div>

      <div class="field">
        <label for="cost">Gemiddelde kosten per vape (€)</label>
        <div class="input-wrapper">
          <input 
            id="cost" 
            placeholder="Bijv. 6.00" 
            type="number" 
            min="0" 
            max="999"
            step="0.01" 
            @keydown="preventInvalid"
            :value="costPerVape" 
            @input="setCost($event)" 
          />
          <div class="input-highlight"></div>
        </div>
      </div>

      <div class="row">
        <div class="field">
          <label for="age">Leeftijd</label>
          <div class="input-wrapper">
            <input 
              id="age" 
              placeholder="18" 
              type="number" 
              min="0" 
              max="999"
              @keydown="preventInvalid"
              :value="age" 
              @input="setAge($event)" 
            />
            <div class="input-highlight"></div>
          </div>
        </div>
        <div class="field">
          <label for="years">Jaren vapen</label>
          <div class="input-wrapper">
            <input 
              id="years" 
              placeholder="1" 
              type="number" 
              min="0" 
              max="999"
              step="0.5" 
              @keydown="preventInvalid"
              :value="yearsVaping" 
              @input="setYears($event)" 
            />
            <div class="input-highlight"></div>
          </div>
        </div>
      </div>
    </form>

    <div class="actions">
      <button class="btn primary big-btn pulse-hover" @click="$emit('start')">
        Bereken Resultaat ✨
      </button>
    </div>
  </section>
</template>

<style scoped>
.input-panel {
  display: flex;
  flex-direction: column;
  gap: 32px;
  color: white;
  position: relative;
}

.settings-btn {
  position: absolute;
  top: -20px;
  right: -20px;
  background: rgba(255, 255, 255, 0.1);
  border: none;
  border-radius: 50%;
  width: 40px;
  height: 40px;
  font-size: 1.2rem;
  cursor: pointer;
  transition: all 0.2s;
  display: flex;
  align-items: center;
  justify-content: center;
  color: var(--text);
}

.settings-btn:hover {
  background: rgba(255, 255, 255, 0.2);
  transform: rotate(45deg);
}

.welcome-text h2 {
  margin: 0 0 8px 0;
  font-size: 1.8rem;
  background: linear-gradient(to right, #fff, #94a3b8);
  -webkit-background-clip: text;
  background-clip: text;
  -webkit-text-fill-color: transparent;
}

.welcome-text p {
  margin: 0;
  color: var(--text-muted);
  font-size: 1.1rem;
}

.fields {
  display: flex;
  flex-direction: column;
  gap: 24px;
}

.field-group {
  display: flex;
  flex-direction: column;
  gap: 12px;
}

.row {
  display: flex;
  gap: 16px;
}

.field {
  flex: 1;
  display: flex;
  flex-direction: column;
  gap: 8px;
}

.field label {
  font-weight: 500;
  font-size: 0.95rem;
  color: var(--text-muted);
  margin-left: 4px;
}

.input-wrapper {
  position: relative;
  border-radius: 12px;
  background: rgba(0, 0, 0, 0.2);
  border: 1px solid rgba(255, 255, 255, 0.1);
  transition: all 0.3s ease;
}

.input-wrapper:focus-within {
  border-color: var(--primary);
  background: rgba(0, 0, 0, 0.3);
  box-shadow: 0 0 0 4px rgba(129, 140, 248, 0.1);
}

.field input {
  width: 100%;
  padding: 16px;
  border-radius: 12px;
  border: none;
  background: transparent;
  color: white;
  font-size: 1.1rem;
  outline: none;
}

.field input::placeholder {
  color: rgba(255, 255, 255, 0.2);
}

.presets {
  display: flex;
  gap: 8px;
}

.chip {
  padding: 8px 16px;
  border-radius: 20px;
  border: 1px solid rgba(255, 255, 255, 0.1);
  background: rgba(255, 255, 255, 0.05);
  color: var(--text-muted);
  cursor: pointer;
  transition: all 0.2s;
  font-size: 0.9rem;
}

.chip:hover {
  background: rgba(255, 255, 255, 0.1);
  color: white;
}

.chip.active {
  background: var(--primary);
  color: white;
  border-color: transparent;
}

.actions {
  margin-top: 8px;
}

.big-btn {
  width: 100%;
  padding: 20px;
  font-size: 1.2rem;
  letter-spacing: 0.5px;
}

.pulse-hover:hover {
  animation: pulse-glow 1.5s infinite;
}

@media (max-width: 600px) {
  .row {
    flex-direction: column;
    gap: 24px;
  }
  
  .welcome-text h2 {
    font-size: 1.5rem;
  }
  
  .input-panel {
    gap: 24px;
  }
}
</style>

