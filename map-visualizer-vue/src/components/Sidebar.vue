<template>
  <aside class="sidebar-section">
    <div class="sidebar-header">
      <div class="actions">
        <button class="btn search-btn" @click="$emit('search')">
          Поиск
        </button>
        <button class="btn clear-btn" @click="$emit('clear')">
          Очистить
        </button>
      </div>
    </div>

    <div class="cards-container">
      <div 
        v-for="item in markers" 
        :key="item.id" 
        class="card"
      >
        <label class="card-label">
          <input 
            type="checkbox" 
            :checked="checkedIds.includes(item.id)" 
            @change="$emit('toggle-item', item.id)"
          />
          <div class="card-info">
            <strong>{{ item.title }}</strong>
            <p v-if="item.description">{{ item.description }}</p>
          </div>
        </label>
      </div>
    </div>
  </aside>
</template>

<script setup>
defineProps({
  markers: {
    type: Array,
    default: () => []
  },
  checkedIds: {
    type: Array,
    default: () => []
  }
})

defineEmits(['clear', 'search', 'toggle-item'])
</script>

<style scoped>
.sidebar-section {
  width: 30%;
  height: 100%;
  background-color: #f8f9fa;
  border-left: 1px solid #e0e0e0;
  padding: 20px;
  box-sizing: border-box;
  display: flex;
  flex-direction: column;
  gap: 20px;
}

.sidebar-header h2 {
  margin: 0 0 12px 0;
  font-size: 20px;
  color: #333;
}

.actions {
  display: flex;
  gap: 10px;
}

.btn {
  flex: 1;
  padding: 10px;
  border: none;
  border-radius: 6px;
  font-size: 14px;
  font-weight: 600;
  cursor: pointer;
  transition: opacity 0.2s ease;
}

.btn:hover {
  opacity: 0.9;
}

.search-btn {
  background-color: #0d6efd;
  color: white;
}

.clear-btn {
  background-color: #dc3545;
  color: white;
}

.cards-container {
  flex: 1;
  overflow-y: auto;
  display: flex;
  flex-direction: column;
  gap: 10px;
}

.card {
  background: white;
  padding: 12px;
  border-radius: 6px;
  border: 1px solid #dee2e6;
}

.card-label {
  display: flex;
  align-items: flex-start;
  gap: 10px;
  cursor: pointer;
}

.card-info strong {
  display: block;
  font-size: 14px;
  color: #212529;
}

.card-info p {
  margin: 4px 0 0 0;
  font-size: 12px;
  color: #6c757d;
}
</style>