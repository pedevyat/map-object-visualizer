<template>
  <main class="page">
    <div class="layout">
      <section class="map-section">
        <OpenStreetMap 
          ref="mapRef"
          :center="[55.7558, 37.6173]" 
          :zoom="8" 
          :markers="visibleMarkers" 
          class="custom-map"
        />
      </section>
      <Sidebar 
        :markers="sidebarMarkers"
        :checked-ids="checkedIds"
        @search="handleSearch"
        @clear="handleClear"
        @toggle-item="toggleItem"
        @select-item="handleSelectItem"
      />
    </div>
  </main>
</template>

<script setup>
import { ref, computed, nextTick } from 'vue'
import OpenStreetMap from '@/components/Map.vue'
import Sidebar from '@/components/Sidebar.vue'
import pointsData from '@/data/mock.json'

const rawMarkers = pointsData.markers || pointsData
const initialData = Array.isArray(rawMarkers) ? rawMarkers : [rawMarkers]

const mapRef = ref(null)
const sidebarMarkers = ref([...initialData])
const checkedIds = ref(initialData.map(item => item.id))


const visibleMarkers = computed(() => {
  return sidebarMarkers.value.filter(item => checkedIds.value.includes(item.id))
})

// Переключение состояния конкретного чекбокса
const toggleItem = (id) => {
  if (checkedIds.value.includes(id)) {
    checkedIds.value = checkedIds.value.filter(itemId => itemId !== id)
  } else {
    checkedIds.value.push(id)
  }
}

// Обработчик клика по карточке
const handleSelectItem = (id) => {
  if (!checkedIds.value.includes(id)) {
    checkedIds.value.push(id)
  }

  nextTick(() => {
    if (mapRef.value) {
      mapRef.value.focusMarker(id)
    }
  })
}

const handleClear = () => {
  sidebarMarkers.value = []
  checkedIds.value = []
}

// восстанавление карточки, включает чекбоксы и плавно масштабирует карту
const handleSearch = async () => {
  sidebarMarkers.value = [...initialData]
  checkedIds.value = initialData.map(item => item.id)

  await nextTick()
  if (mapRef.value) {
    mapRef.value.fitToMarkers()
  }
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