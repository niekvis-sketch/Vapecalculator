<script setup>
import { toRefs } from 'vue'
const props = defineProps({
  weeklyVapes: { type: Number, required: true },
  costPerVape: { type: Number, required: true },
  age: { type: [Number, String], default: '' },
  yearsVaping: { type: [Number, String], default: '' }
})
const emit = defineEmits(['update:weeklyVapes', 'update:costPerVape', 'update:age', 'update:yearsVaping', 'start'])

function setWeekly(v) { emit('update:weeklyVapes', Number(v)) }
function setCost(v) { emit('update:costPerVape', Number(v)) }
function setAge(v) { emit('update:age', v) }
function setYears(v) { emit('update:yearsVaping', v) }
</script>

<template>
  <section class="panel input-panel">
    <div class="welcome-text">
      <h2>Ontdek jouw Vape-profiel</h2>
      <p>Vul je gegevens in en zie wat je écht uitgeeft (en wat je mist).</p>
    </div>
    <form class="fields" @submit.prevent>
      <div class="field">
        <label for="weekly">Aantal vapes per week</label>
        <input id="weekly" placeholder="Bijv. 2" type="number" min="0" :value="weeklyVapes" @input="setWeekly($event.target.value)" />
      </div>

      <div class="field">
        <label for="cost">Gemiddelde kosten per vape (€)</label>
        <input id="cost" placeholder="Bijv. 6.00" type="number" min="0" step="0.01" :value="costPerVape" @input="setCost($event.target.value)" />
      </div>

      <div class="row">
        <div class="field">
          <label for="age">Leeftijd (optioneel)</label>
          <input id="age" placeholder="18" type="number" min="0" :value="age" @input="setAge($event.target.value)" />
        </div>
        <div class="field">
          <label for="years">Aantal jaar aan het vapen (optioneel)</label>
          <input id="years" placeholder="Bijv. 1" type="number" min="0" step="0.5" :value="yearsVaping" @input="setYears($event.target.value)" />
        </div>
      </div>

      <div class="presets" aria-hidden="false">
        <span class="muted">Snel kiezen:</span>
        <div class="btns">
          <button class="btn ghost" @click.prevent="setWeekly(1)">1/wk</button>
          <button class="btn ghost" @click.prevent="setWeekly(2)">2/wk</button>
          <button class="btn ghost" @click.prevent="setWeekly(3)">3/wk</button>
        </div>
      </div>
    </form>

    <div class="actions">
      <button class="btn primary big-btn" @click="$emit('start')">Start jouw Wrapped</button>
    </div>
  </section>
</template>

<style scoped>
.input-panel{display:flex;flex-direction:column;gap:20px;}
.welcome-text h2 { margin: 0 0 8px 0; font-size: 1.5rem; }
.welcome-text p { margin: 0; color: var(--muted); }

.fields{display:flex;flex-direction:column;gap:16px}
.row { display: flex; gap: 12px; }
.field{flex:1;}
.field label{display:block;margin-bottom:6px;font-weight:600;font-size:0.9rem}
.field input{width:100%;padding:12px;border-radius:10px;border:1px solid var(--muted-border);background:var(--input-bg);font-size:1rem}

.presets{display:flex;flex-direction:column;gap:8px;margin-top:4px}
.presets .btns{display:flex;gap:8px;flex-wrap:wrap}

.btns button{padding:6px 12px;border-radius:8px;border:1px solid var(--muted-border);background:var(--surface);cursor:pointer;font-size:0.85rem}
.btns button:hover{background:var(--accent);color:white;border-color:var(--accent)}

.actions { margin-top: 10px; }
.big-btn { width: 100%; padding: 14px; font-size: 1.1rem; font-weight: bold; border-radius: 12px; }
</style>
