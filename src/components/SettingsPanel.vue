<script setup>
import { ref, watch } from 'vue'

const props = defineProps({
  modelValue: {
    type: Object,
    required: true
  },
  theme: {
    type: String,
    required: true
  },
  themes: {
    type: Object,
    required: true
  }
})

const emit = defineEmits(['update:modelValue', 'update:theme', 'close'])

const settings = ref({ ...props.modelValue })
const activeTab = ref('slides') // 'slides' | 'theme'

const slides = [
  { id: 'costs', label: 'Kosten Overzicht' },
  { id: 'comparisons', label: 'Vergelijkingen' },
  { id: 'time', label: 'Tijd Verspild' },
  { id: 'health', label: 'Gezondheid' },
  { id: 'future', label: 'Toekomstperspectief' },
  { id: 'stats', label: 'Statistieken' },
  { id: 'savings', label: 'Besparingen' },
  { id: 'positive', label: 'Positief Nieuws' },
  { id: 'action', label: 'Actieplan' }
]

watch(settings, (newVal) => {
  emit('update:modelValue', newVal)
}, { deep: true })

function selectTheme(key) {
  emit('update:theme', key)
}
</script>

<template>
  <div class="settings-overlay">
    <div class="settings-card glass">
      <div class="settings-header">
        <h2>Instellingen</h2>
        <button class="close-btn" @click="$emit('close')">×</button>
      </div>

      <div class="tabs">
        <button 
          class="tab-btn" 
          :class="{ active: activeTab === 'slides' }"
          @click="activeTab = 'slides'"
        >
          Slides
        </button>
        <button 
          class="tab-btn" 
          :class="{ active: activeTab === 'theme' }"
          @click="activeTab = 'theme'"
        >
          Thema
        </button>
      </div>
      
      <div v-if="activeTab === 'slides'" class="tab-content">
        <p class="desc">Kies welke slides je wilt zien in je Wrapped:</p>
        
        <div class="toggles-list">
          <label v-for="slide in slides" :key="slide.id" class="toggle-item">
            <span>{{ slide.label }}</span>
            <input 
              type="checkbox" 
              v-model="settings[slide.id]"
            >
            <span class="toggle-switch"></span>
          </label>
        </div>
      </div>

      <div v-else class="tab-content">
        <p class="desc">Kies een kleurenthema:</p>
        <div class="themes-list">
          <button 
            v-for="(t, key) in themes" 
            :key="key"
            class="theme-btn"
            :class="{ active: theme === key }"
            @click="selectTheme(key)"
          >
            <div class="theme-preview" :style="{ background: t.colors['--bg'] }">
              <div class="dot" :style="{ background: t.colors['--primary'] }"></div>
            </div>
            <span>{{ t.name }}</span>
            <div v-if="theme === key" class="check">✓</div>
          </button>
        </div>
        <p v-if="!themes || Object.keys(themes).length === 0" class="desc">
          Geen thema's gevonden.
        </p>
      </div>

      <button class="btn primary full-width" @click="$emit('close')">Opslaan & Sluiten</button>
    </div>
  </div>
</template>

<style scoped>
.settings-overlay {
  position: fixed;
  inset: 0;
  background: rgba(0, 0, 0, 0.6);
  backdrop-filter: blur(5px);
  z-index: 300;
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 20px;
}

.settings-card {
  width: 100%;
  max-width: 400px;
  padding: 24px;
  border-radius: 24px;
  background: var(--surface);
  border: 1px solid var(--border);
  display: flex;
  flex-direction: column;
  max-height: 85vh;
}

.settings-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 16px;
}

.settings-header h2 {
  margin: 0;
  font-size: 1.5rem;
}

.tabs {
  display: flex;
  gap: 8px;
  margin-bottom: 20px;
  background: rgba(0,0,0,0.2);
  padding: 4px;
  border-radius: 12px;
}

.tab-btn {
  flex: 1;
  padding: 8px;
  border: none;
  background: transparent;
  color: var(--text-muted);
  border-radius: 8px;
  cursor: pointer;
  font-weight: 600;
  transition: all 0.2s;
}

.tab-btn.active {
  background: var(--surface-highlight);
  color: var(--text-main);
  box-shadow: 0 2px 4px rgba(0,0,0,0.1);
}

.tab-content {
  flex: 1;
  overflow-y: auto;
  margin-bottom: 20px;
}

.close-btn {
  background: none;
  border: none;
  color: var(--text-main);
  font-size: 2rem;
  cursor: pointer;
  padding: 0;
  line-height: 1;
}

.desc {
  color: var(--text-muted);
  margin-bottom: 16px;
}

.toggles-list {
  display: flex;
  flex-direction: column;
  gap: 12px;
}

.toggle-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 12px;
  background: rgba(255, 255, 255, 0.05);
  border-radius: 12px;
  cursor: pointer;
  transition: background 0.2s;
}

.toggle-item:hover {
  background: rgba(255, 255, 255, 0.1);
}

/* Themes List */
.themes-list {
  display: flex;
  flex-direction: column;
  gap: 12px;
}

.theme-btn {
  display: flex;
  align-items: center;
  gap: 16px;
  padding: 12px;
  background: rgba(255, 255, 255, 0.05);
  border: 1px solid transparent;
  border-radius: 12px;
  cursor: pointer;
  color: var(--text-main);
  transition: all 0.2s;
  width: 100%;
  text-align: left;
}

.theme-btn:hover {
  background: rgba(255, 255, 255, 0.1);
}

.theme-btn.active {
  background: var(--surface-highlight);
  border-color: var(--primary);
}

.theme-preview {
  width: 40px;
  height: 40px;
  border-radius: 50%;
  border: 2px solid rgba(255,255,255,0.2);
  display: flex;
  align-items: center;
  justify-content: center;
}

.theme-preview .dot {
  width: 16px;
  height: 16px;
  border-radius: 50%;
}

.check {
  margin-left: auto;
  color: var(--primary);
  font-weight: bold;
}

/* Custom Toggle Switch */
.toggle-item input {
  display: none;
}

.toggle-switch {
  position: relative;
  width: 44px;
  height: 24px;
  background: rgba(255, 255, 255, 0.2);
  border-radius: 12px;
  transition: 0.3s;
}

.toggle-switch::after {
  content: '';
  position: absolute;
  top: 2px;
  left: 2px;
  width: 20px;
  height: 20px;
  background: white;
  border-radius: 50%;
  transition: 0.3s;
}

input:checked + .toggle-switch {
  background: var(--primary);
}

input:checked + .toggle-switch::after {
  transform: translateX(20px);
}

.full-width {
  width: 100%;
}
</style>
