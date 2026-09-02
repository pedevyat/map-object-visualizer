<template>
  <div class="map-container" ref="mapContainer"></div>
</template>

<script setup>
import { ref, onMounted, onUnmounted, watch } from 'vue'
import L from 'leaflet'
import 'leaflet/dist/leaflet.css'
import { getColorFromTitle } from '@/utils/color'

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

// функция для центрирования карты по маркерам
const centerMapByMarkers = (animate = true) => {
  if (!map || !props.markers) return

  const list = Array.isArray(props.markers) 
    ? props.markers 
    : (props.markers.markers || [props.markers])

  const validCoords = list
    .filter(item => item && item.latitude !== undefined && item.longitude !== undefined)
    .map(item => [item.latitude, item.longitude])

  if (validCoords.length === 0) return

  if (validCoords.length === 1) {
    map.setView(validCoords[0], props.zoom, { animate })
    return
  }
  const bounds = L.latLngBounds(validCoords)

  if (bounds.isValid()) {
    map.fitBounds(bounds, { 
      padding: [50, 50], 
      animate, 
      duration: animate ? 1 : 0 
    })
  }
}

const initMap = () => {
  map = L.map(mapContainer.value).setView(props.center, props.zoom)

  L.tileLayer('https://tile.openstreetmap.org/{z}/{x}/{y}.png', {
    maxZoom: 19,
    attribution: '&copy; <a href="http://www.openstreetmap.org/copyright">OpenStreetMap</a>'
  }).addTo(map)

  markerLayerGroup = L.layerGroup().addTo(map)
  renderMarkers()
  centerMapByMarkers(false)
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

const markerInstances = {}

const renderMarkers = () => {
  if (!markerLayerGroup) return
  markerLayerGroup.clearLayers()

  Object.keys(markerInstances).forEach(key => delete markerInstances[key])

  if (!props.markers) return

  const rawList = Array.isArray(props.markers) 
    ? props.markers 
    : (props.markers.markers || [props.markers])

  const validItems = rawList.filter(
    (item) => item && item.latitude !== undefined && item.longitude !== undefined
  )

  const groups = {}

  validItems.forEach((item, originalIndex) => {
    const titleKey = item.title

    if (!groups[titleKey]) {
      groups[titleKey] = []
    }
    groups[titleKey].push({
      ...item,
      _sortKey: item.id !== undefined ? item.id : originalIndex
    })
  })

  Object.entries(groups).forEach(([title, items]) => {
    items.sort((a, b) => (a._sortKey > b._sortKey ? 1 : -1))

    const groupColor = getColorFromTitle(title)
    const customIcon = createCustomIcon(groupColor)
    const latLngs = []

    items.forEach((item) => {
      const coords = [item.latitude, item.longitude]
      latLngs.push(coords)

      const marker = L.marker(coords, { icon: customIcon })
      const popupContent = `<b>${item.title || ''}</b><br>${item.description || ''}`
      marker.bindPopup(popupContent)
      
      marker.addTo(markerLayerGroup)

      // Сохраняем ссылку на маркер по его id
      if (item.id !== undefined) {
        markerInstances[item.id] = marker
      }
    })

    if (latLngs.length >= 2) {
      const polyline = L.polyline(latLngs, {
        color: groupColor,
        weight: 3,
        opacity: 0.8,
        smoothFactor: 1
      })

      polyline.addTo(markerLayerGroup)
    }
  })
}

// метод для центрирования карты и открытия попапа конкретного маркера
const focusMarker = (id) => {
  const targetMarker = markerInstances[id]
  if (!targetMarker || !map) return

  const latLng = targetMarker.getLatLng()
  targetMarker.openPopup()
  map.panTo(latLng, { animate: true, duration: 0.8 })
}

defineExpose({ 
  fitToMarkers: () => centerMapByMarkers(true),
  centerMapByMarkers,
  focusMarker
})

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
  height: 100%;
  overflow: hidden;
}
</style>