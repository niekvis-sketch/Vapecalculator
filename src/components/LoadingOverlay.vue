<template>
  <div class="loading-overlay" role="status" aria-live="polite">
    <div class="spinner-container">
      <div class="spinner-ring"></div>
      <div class="spinner-ring delay"></div>
      <div class="spinner-core"></div>
    </div>
    <div class="label">Jouw jaar in kaart brengen...</div>
  </div>
</template>

<script setup>
</script>

<style scoped>
.loading-overlay {
  position: fixed;
  inset: 0;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  background: rgba(15, 23, 42, 0.8);
  backdrop-filter: blur(10px);
  z-index: 60;
  color: white;
}

.spinner-container {
  position: relative;
  width: 80px;
  height: 80px;
  margin-bottom: 24px;
}

.spinner-ring {
  position: absolute;
  inset: 0;
  border-radius: 50%;
  border: 4px solid transparent;
  border-top-color: var(--primary);
  border-right-color: var(--secondary);
  animation: spin 1.5s cubic-bezier(0.4, 0, 0.2, 1) infinite;
}

.spinner-ring.delay {
  width: 60px;
  height: 60px;
  top: 10px;
  left: 10px;
  border-top-color: var(--accent);
  border-right-color: transparent;
  animation-direction: reverse;
  animation-duration: 2s;
}

.spinner-core {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  width: 12px;
  height: 12px;
  background: white;
  border-radius: 50%;
  box-shadow: 0 0 20px white;
  animation: pulse-glow 1s infinite;
}

.label {
  font-weight: 600;
  font-size: 1.1rem;
  letter-spacing: 0.5px;
  opacity: 0.9;
  animation: float 3s ease-in-out infinite;
}

@keyframes spin {
  to { transform: rotate(360deg); }
}
</style>

