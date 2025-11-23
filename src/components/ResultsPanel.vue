<script setup>
import ComparisonItem from './ComparisonItem.vue'
const props = defineProps({ weeklyCost: Number, monthlyCost: Number, yearlyCost: Number, comparisons: Array })

const formatter = new Intl.NumberFormat('nl-NL', { style: 'currency', currency: 'EUR' })
function fmt(v){ return formatter.format(v) }
function eqCount(amount, itemCost){ if (!itemCost) return 0; return Math.max(0, Math.floor(amount / itemCost)) }
</script>

<template>
  <section class="panel results">
    <div class="card">
      <h3>Per week</h3>
      <p class="amount">{{ fmt(weeklyCost) }}</p>
      <ul class="list">
        <ComparisonItem v-for="it in comparisons" :key="it.id" :item="it" :amount="eqCount(weeklyCost, it.cost)"/>
      </ul>
    </div>

    <div class="card">
      <h3>Per maand</h3>
      <p class="amount">{{ fmt(monthlyCost) }}</p>
      <ul class="list">
        <ComparisonItem v-for="it in comparisons" :key="it.id" :item="it" :amount="eqCount(monthlyCost, it.cost)"/>
      </ul>
    </div>

    <div class="card highlight">
      <h3>Per jaar</h3>
      <p class="amount accent">{{ fmt(yearlyCost) }}</p>
      <ul class="list">
        <ComparisonItem v-for="it in comparisons" :key="it.id" :item="it" :amount="eqCount(yearlyCost, it.cost)"/>
      </ul>
    </div>
  </section>
</template>

<style scoped>
.results{display:grid;grid-template-columns:repeat(auto-fit,minmax(220px,1fr));gap:14px}
.card{background:var(--surface);border:1px solid var(--card-border);padding:14px;border-radius:10px}
.card h3{margin:0 0 6px;font-size:1rem}
.amount{font-weight:800;font-size:1.25rem;margin:0 0 10px}
.amount.accent{color:var(--accent)}
.list{list-style:none;padding:0;margin:0;display:flex;flex-direction:column;gap:8px}
.highlight{border-color:var(--accent);box-shadow:0 12px 36px rgba(79,142,247,0.08)}
</style>
