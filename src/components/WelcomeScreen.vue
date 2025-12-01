<script setup>
defineEmits(['start'])
</script>

<template>
  <div class="welcome-screen">
    <h1>Wat zou je liever?</h1>
    <p class="subtitle">Ontdek in 4 stappen wat vapen je kost</p>
    
    <div class="steps-preview">
      <div class="step-dot">1</div>
      <div class="line"></div>
      <div class="step-dot">2</div>
      <div class="line"></div>
      <div class="step-dot">3</div>
      <div class="line"></div>
      <div class="step-dot">4</div>
    </div>

    <button class="btn primary big pulse-hover" @click="$emit('start')">
      Start de test
    </button>
  </div>
</template>

<style scoped>
.welcome-screen {
  text-align: center;
  padding: 40px 20px;
  max-width: 600px;
  margin: 0 auto;
}

h1 {
  font-size: 3.5rem;
  margin-bottom: 16px;
  background: linear-gradient(135deg, #fff 0%, #94a3b8 100%);
  -webkit-background-clip: text;
  background-clip: text;
  -webkit-text-fill-color: transparent;
}

.subtitle {
  font-size: 1.5rem;
  color: var(--text-muted);
  margin-bottom: 48px;
}

.steps-preview {
  display: flex;
  align-items: center;
  justify-content: center;
  margin-bottom: 48px;
  gap: 8px;
}

.step-dot {
  width: 40px;
  height: 40px;
  border-radius: 50%;
  background: var(--surface-highlight);
  display: flex;
  align-items: center;
  justify-content: center;
  font-weight: bold;
  color: var(--primary);
  border: 2px solid var(--primary);
}

.line {
  width: 40px;
  height: 2px;
  background: var(--surface-highlight);
}

.btn.big {
  padding: 20px 48px;
  font-size: 1.5rem;
  border-radius: 50px;
}

.pulse-hover:hover {
  animation: pulse-glow 1.5s infinite;
}
</style>
