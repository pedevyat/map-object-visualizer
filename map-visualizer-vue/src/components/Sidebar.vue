<template>
  <aside class="sidebar-section">
    <div class="sidebar-header">
      <button class="clear-btn" @click="$emit('clear')">
        Очистить
      </button>
    </div>
  </aside>
</template>

<script setup>
defineEmits(['clear'])
</script>

<style scoped>
.sidebar-section {
  width: 30%;
  height: 100%;
  background-color: #f8f9fa;
  border-left: 1px solid #e0e0e0;
  padding: 20px;
  box-sizing: border-box;
}

.sidebar-header {
  display: flex;
  flex-direction: column;
  gap: 16px;
}

.sidebar-header h2 {
  margin: 0;
  font-size: 20px;
  color: #333;
}

.clear-btn {
  padding: 10px 16px;
  background-color: #dc3545;
  color: white;
  border: none;
  border-radius: 6px;
  font-size: 14px;
  font-weight: 600;
  cursor: pointer;
  transition: background-color 0.2s ease;
}

.clear-btn:hover {
  background-color: #c82333;
}
</style>