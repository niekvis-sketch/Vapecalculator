<script setup>
const props = defineProps({ item: { type: Object, required: true }, amount: { type: Number, required: true } })
</script>

<template>
  <li class="comparison-item" v-if="amount > 0">
    <span class="amt">{{ amount }}</span>
    <span class="name">
      <span v-if="item.icon" class="icon">{{ item.icon }}</span>
      {{ item.name }}
    </span>
  </li>
</template>

<style scoped>
.comparison-item{display:flex;gap:12px;align-items:center}
.comparison-item .amt{display:inline-block;background:var(--bg);padding:4px 8px;border-radius:999px;border:1px solid var(--muted-border);font-weight:700;font-size: 0.9rem;}
.comparison-item .name{color:var(--muted);font-size:0.9rem; display: flex; align-items: center; gap: 6px;}
.icon { font-size: 1.1rem; }
</style>
