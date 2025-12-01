<script setup>
defineProps({
  question: {
    type: Object,
    required: true
  },
  step: {
    type: Number,
    required: true
  },
  totalSteps: {
    type: Number,
    required: true
  }
})

defineEmits(['select'])
</script>

<template>
  <div class="preference-screen">
    <div class="step-indicator">Vraag {{ step }} van {{ totalSteps }}</div>
    <h2>{{ question.title }}</h2>
    
    <div class="options-grid">
      <button 
        v-for="option in question.options" 
        :key="option.id"
        class="option-card glass"
        @click="$emit('select', option)"
      >
        <div class="icon">{{ option.icon }}</div>
        <div class="label">{{ option.name }}</div>
      </button>
    </div>
  </div>
</template>

<style scoped>
.preference-screen {
  text-align: center;
  width: 100%;
  max-width: 600px;
  margin: 0 auto;
  padding: 20px;
  animation: fadeIn 0.5s ease;
}

.step-indicator {
  color: var(--primary);
  font-weight: 600;
  margin-bottom: 16px;
  text-transform: uppercase;
  letter-spacing: 1px;
  font-size: 0.9rem;
}

h2 {
  font-size: 2rem;
  margin-bottom: 40px;
  color: var(--text-main);
}

.options-grid {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 20px;
}

.option-card {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  padding: 32px 20px;
  border: 2px solid transparent;
  cursor: pointer;
  transition: all 0.3s cubic-bezier(0.175, 0.885, 0.32, 1.275);
  background: rgba(255, 255, 255, 0.05);
  border-radius: 24px;
}

.option-card:hover {
  transform: translateY(-5px);
  background: rgba(255, 255, 255, 0.1);
  border-color: var(--primary);
}

.option-card:active {
  transform: scale(0.95);
}

.icon {
  font-size: 4rem;
  margin-bottom: 16px;
}

.label {
  font-size: 1.2rem;
  font-weight: 600;
  color: var(--text-main);
}

@keyframes fadeIn {
  from { opacity: 0; transform: translateY(10px); }
  to { opacity: 1; transform: translateY(0); }
}

@media (max-width: 500px) {
  .options-grid {
    grid-template-columns: 1fr;
  }
}
</style>
