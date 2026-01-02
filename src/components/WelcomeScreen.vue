<script setup>
import cloudBg from '../assets/cloud-bg.svg'

const emit = defineEmits(['start'])
</script>

<template>
  <div class="welcome-container">
    <!-- Background Noise Texture -->
    <div class="noise-overlay"></div>

    <!-- Cloud Background -->
    <img :src="cloudBg" class="cloud-bg" alt="" />

    <!-- Floating Euros -->
    <div class="euro euro-1">€</div>
    <div class="euro euro-2">€</div>
    <div class="euro euro-3">€</div>

    <!-- Main Content -->
    <div class="content">
      <h1 class="title">
        <span class="line line-1">LAAT JE</span>
        <span class="line line-2">GELD</span>
        <span class="line line-3">NIET IN</span>
        <span class="line line-4">ROOK</span>
        <span class="line line-5">OPGAAN</span>
      </h1>

      <p class="subtitle">CHECK IN EEN PAAR STAPPEN WAT VAPEN JE KOST</p>

      <button class="start-btn" @click="$emit('start')">
        START
      </button>
    </div>
  </div>
</template>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Inter:ital,wght@1,900&display=swap');

.welcome-container {
  position: relative;
  width: 100%;
  height: 100vh;
  background-color: #2563EB; /* Bright Blue */
  overflow: hidden;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  font-family: 'Inter', sans-serif;
}

.cloud-bg {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  min-width: 100%;
  min-height: 100%;
  width: auto;
  height: auto;
  object-fit: cover;
  z-index: 0;
  opacity: 0.8;
}

.content {
  position: relative;
  z-index: 10;
  text-align: center;
  display: flex;
  flex-direction: column;
  align-items: center;
  width: 100%;
  max-width: 500px;
  padding: 20px;
}

.title {
  display: flex;
  flex-direction: column;
  align-items: center;
  font-style: italic;
  font-weight: 900;
  font-size: 4rem;
  line-height: 0.9;
  color: #F1F5F9;
  text-shadow: 3px 3px 0px rgba(0,0,0,0.1);
  transform: rotate(-5deg);
  margin-bottom: 2rem;
}

.line {
  display: block;
  text-transform: uppercase;
}

.line-1 { font-size: 0.8em; transform: translateX(-20px); }
.line-2 { font-size: 1.1em; transform: translateX(10px); }
.line-3 { font-size: 0.9em; transform: translateX(-10px); }
.line-4 { font-size: 1.2em; transform: translateX(15px); }
.line-5 { font-size: 1.0em; transform: translateX(-5px); }

.subtitle {
  color: white;
  font-weight: 700;
  font-size: 1rem;
  text-transform: uppercase;
  margin-bottom: 3rem;
  max-width: 80%;
  line-height: 1.4;
  text-shadow: 0 2px 4px rgba(0,0,0,0.2);
}

.start-btn {
  background-color: #D9F904; /* Neon Yellow */
  color: black;
  font-family: 'Inter', sans-serif;
  font-weight: 900;
  font-size: 1.5rem;
  padding: 16px 64px;
  border: none;
  cursor: pointer;
  text-transform: uppercase;
  box-shadow: 8px 8px 0px white; /* Hard shadow offset */
  transition: transform 0.1s, box-shadow 0.1s;
}

.start-btn:active {
  transform: translate(4px, 4px);
  box-shadow: 4px 4px 0px white;
}

/* Floating Euros */
.euro {
  position: absolute;
  color: #D9F904;
  font-weight: 900;
  font-style: italic;
  z-index: 5;
  animation: float 4s ease-in-out infinite;
}

.euro-1 {
  font-size: 6rem;
  top: 10%;
  left: 10%;
  transform: rotate(-15deg);
  animation-delay: 0s;
}

.euro-2 {
  font-size: 5rem;
  top: 25%;
  right: 10%;
  transform: rotate(15deg);
  animation-delay: 1s;
}

.euro-3 {
  font-size: 4rem;
  bottom: 35%;
  right: 20%;
  transform: rotate(-10deg);
  animation-delay: 2s;
}

@keyframes float {
  0%, 100% { transform: translateY(0) rotate(var(--r, 0deg)); }
  50% { transform: translateY(-20px) rotate(var(--r, 0deg)); }
}

/* Mobile Responsiveness */
@media (max-width: 600px) {
  .title { font-size: 3rem; }
  .euro-1 { font-size: 4rem; top: 5%; left: 5%; }
  .euro-2 { font-size: 3.5rem; top: 20%; right: 5%; }
  .euro-3 { font-size: 3rem; bottom: 30%; right: 15%; }
}
</style>
