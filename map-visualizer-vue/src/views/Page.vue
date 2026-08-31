<template>
  <main class="page">
    <div class="layout">
      <section class="map-section">
        <OpenStreetMap 
          :center="[59.934280, 30.335098]" 
          :zoom="12" 
          :markers="pointList" 
          class="custom-map"
        />
      </section>

      <aside class="sidebar-section"></aside>
    </div>
  </main>
</template>

<script setup>
import { ref } from 'vue'
import OpenStreetMap from '@/components/Map.vue'

const pointList = ref([
  { lat: 59.934280, lng: 30.335098, title: 'Невский проспект' },
  { lat: 59.940114, lng: 30.328904, title: 'Эрмитаж' }
])
</script>

<style>
html, body, #app {
  margin: 0 !important;
  padding: 0 !important;
  width: 100vw !important;
  height: 100vh !important;
  max-width: none !important;
  overflow: hidden !important;
}
</style>

<style scoped>
.page {
  width: 100vw;
  height: 100vh;
  margin: 0;
  padding: 0;
}

.layout {
  display: flex;
  width: 100%;
  height: 100%;
}

.map-section {
  width: 70%;
  height: 100%;
}

.sidebar-section {
  width: 30%;
  height: 100%;
}

:deep(.map-wrapper),
:deep(.map-container) {
  width: 100%;
  height: 100% !important;
  border-radius: 0 !important;
}
</style>