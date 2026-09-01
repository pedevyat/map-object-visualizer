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

// цвет маркера в зависимости от title
const getColorFromTitle = (title) => {
  // Константы алгоритма FNV-1a (32-bit)
  let hash = 0x811c9dc5
  for (let i = 0; i < title.length; i++) {
    hash ^= title.charCodeAt(i)
    hash += (hash << 1) + (hash << 4) + (hash << 7) + (hash << 8) + (hash << 24)
  }

  // Принудительно переводим в 32-битное беззнаковое целое
  hash = hash >>> 0

  // Извлекаем RGB каналы из байтов хеша
  const r = (hash & 0xFF0000) >> 16
  const g = (hash & 0x00FF00) >> 8
  const b = hash & 0x0000FF

  // Переводим в HEX
  const toHex = (c) => c.toString(16).padStart(2, '0')
  return `#${toHex(r)}${toHex(g)}${toHex(b)}`
}

// Создание SVG пина нужного цвета
const createCustomIcon = (color) => {
  const svgTemplate = `
    <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" width="30" height="30">
      <path fill="${color}" stroke="#FFFFFF" stroke-width="1.5" d="M12 2C8.13 2 5 5.13 5 9c0 5.25 7 13 7 13s7-7.75 7-13c0-3.87-3.13-7-7-7zm0 9.5c-1.38 0-2.5-1.12-2.5-2.5s1.12-2.5 2.5-2.5 2.5 1.12 2.5 2.5-1.12 2.5-2.5 2.5z"/>
    </svg>`

  return L.divIcon({
    className: 'custom-svg-icon',
    html: svgTemplate,
    iconSize: [30, 30],
    iconAnchor: [15, 30],
    popupAnchor: [0, -30]
  })
}

const renderMarkers = () => {
  if (!markerLayerGroup) return
  markerLayerGroup.clearLayers()

  // Если props.markers не передан или null — выходим
  if (!props.markers) return

  // Если пришел один объект вместо массива — оборачиваем его в массив [props.markers]
  const list = Array.isArray(props.markers) ? props.markers : [props.markers]

  list.forEach((item) => {
    if (item && item.latitude && item.longitude) {
      const markerColor = getColorFromTitle(item.title)
      const customIcon = createCustomIcon(markerColor)

      const marker = L.marker([item.latitude, item.longitude], { icon: customIcon })

      const popupContent = `<b>${item.title || ''}</b><br>${item.description || ''}`
      marker.bindPopup(popupContent)

      marker.addTo(markerLayerGroup)
    }
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