<template>
  <div class="map-container" ref="mapContainer"></div>
</template>

<script setup>
import { ref, onMounted, onUnmounted, watch } from 'vue'
import L from 'leaflet'
import 'leaflet/dist/leaflet.css'

const props = defineProps({
  center: {
    type: Array,
    default: () => [55.751244, 37.618423]
  },
  zoom: {
    type: Number,
    default: 13
  },
  markers: {
    type: Array,
    default: () => [] 
  }
})

const mapContainer = ref(null)
let map = null
let markerLayerGroup = null

const initMap = () => {
  map = L.map(mapContainer.value).setView(props.center, props.zoom)
  L.tileLayer('https://tile.openstreetmap.org/{z}/{x}/{y}.png', {
    maxZoom: 19,
    attribution: '&copy; <a href="http://www.openstreetmap.org/copyright">OpenStreetMap</a>'
  }).addTo(map)

  markerLayerGroup = L.layerGroup().addTo(map)
  renderMarkers()
}

const renderMarkers = () => {
  if (!markerLayerGroup) return
  markerLayerGroup.clearLayers()

  props.markers.forEach((item) => {
    const marker = L.marker([item.lat, item.lng])
    if (item.title) {
      marker.bindPopup(item.title)
    }
    marker.addTo(markerLayerGroup)
  })
}


watch(() => props.markers, renderMarkers, { deep: true })

onMounted(() => {
  initMap()
})


onUnmounted(() => {
  if (map) {
    map.remove()
  }
})
</script>

<style scoped>
.map-container {
  width: 100%;
  height: 400px;
  border-radius: 8px;
  overflow: hidden;
}
</style>