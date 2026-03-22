<template>
  <div class="filters">
    <input v-model="searchQuery" type="text" placeholder="Поиск по названию или автору..." />
    <div class="filter-buttons">
      <button v-for="option in filterOptions" :key="option.value"
        @click="$emit('update:filter', option.value)"
        :class="{ active: filter === option.value }">
        {{ option.label }}
      </button>
    </div>
    <div class="stats">
      <p>Всего: {{ total }} | Прочитано: {{ completed }} | Осталось: {{ total - completed }}</p>
    </div>
  </div>
</template>

<script setup>
import { computed, ref } from 'vue'
const props = defineProps(['filter','books'])
defineEmits(['update:filter'])
const searchQuery = ref('')
const filterOptions = [{value:'all',label:'Все'},{value:'unread',label:'Непрочитанные'},{value:'read',label:'Прочитанные'}]
const total = computed(()=>props.books.length)
const completed = computed(()=>props.books.filter(b=>b.completed).length)
</script>

<style scoped>
.filters { background:white; padding:20px; border-radius:8px; margin-bottom:20px; }
.filter-buttons button.active { background:#4CAF50; color:white; }
</style>