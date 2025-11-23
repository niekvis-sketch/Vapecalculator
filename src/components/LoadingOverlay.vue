<template>
  <div class="loading-overlay" role="status" aria-live="polite">
    <div class="spinner" aria-hidden="true"></div>
    <div class="label">Even rekenen…</div>
  </div>
</template>

<script setup>
</script>

<style scoped>
.loading-overlay{position:fixed;inset:0;display:flex;flex-direction:column;align-items:center;justify-content:center;background:rgba(15,23,42,0.45);backdrop-filter:blur(4px);z-index:60;color:white}
.spinner{width:68px;height:68px;border-radius:50%;border:8px solid rgba(255,255,255,0.14);border-top-color:white;animation:spin 1s linear infinite}
.label{margin-top:12px;font-weight:600}
@keyframes spin{to{transform:rotate(360deg)}}
</style>
