<template>
  <main class="page">
    <div class="layout">
      <section class="map-section">
        <OpenStreetMap 
          :center="[55.7558, 37.6173]" 
          :zoom="8" 
          :markers="pointList.markers" 
          class="custom-map"
        />
      </section>

      <Sidebar @clear="clearMarkers" />
    </div>
  </main>
</template>

<script setup>
import { ref } from 'vue'
import OpenStreetMap from '@/components/Map.vue'
import Sidebar from '@/components/Sidebar.vue'
import pointsData from '@/data/mock.json'

const pointList = ref(pointsData)
const clearMarkers = () => {
  pointList.value = []
}
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