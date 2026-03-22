<template>
  <div class="filters card shadow-sm mb-4">
    <div class="card-body">
      <!-- Поисковая строка -->
      <div class="mb-3">
        <input 
          v-model="searchQuery" 
          type="text" 
          class="form-control" 
          placeholder="Поиск по названию или автору..." 
        />
      </div>

      <!-- Кнопки фильтров -->
      <div class="d-flex flex-wrap gap-2 justify-content-between align-items-center">
        <div class="filter-buttons d-flex flex-wrap gap-2">
          <button
            v-for="option in filterOptions"
            :key="option.value"
            @click="$emit('update:filter', option.value)"
            :class="[
              'btn',
              currentFilter === option.value ? 'btn-success' : 'btn-outline-secondary'
            ]"
          >
            {{ option.label }}
          </button>
        </div>

        <!-- Статистика (на мобильных будет под кнопками) -->
        <div class="stats small text-muted mt-2 mt-md-0">
          Всего: <strong>{{ total }}</strong> | Прочитано: <strong>{{ completed }}</strong> | Осталось: <strong>{{ total - completed }}</strong>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { computed } from 'vue'

const props = defineProps(['filter', 'books'])
defineEmits(['update:filter'])

// Используем defineModel для двусторонней связи
const searchQuery = defineModel('searchQuery')

const filterOptions = [
  { value: 'all', label: 'Все' },
  { value: 'unread', label: 'Непрочитанные' },
  { value: 'read', label: 'Прочитанные' }
]

const total = computed(() => props.books.length)
const completed = computed(() =>
  props.books.filter(b => b.completed).length
)
</script>

<style scoped>
/* Убираем лишние стили, если они дублируют классы Bootstrap */
.filters {
  background: white;
}

.filter-buttons {
  /* Гарантируем, что кнопки переносятся, если места мало */
  flex-wrap: wrap;
}

/* Адаптивность для статистики: на узких экранах она уходит вниз */
@media (max-width: 576px) {
  .stats {
    width: 100%;
    text-align: left;
    margin-top: 10px !important;
    border-top: 1px solid #eee;
    padding-top: 10px;
  }
}
</style>