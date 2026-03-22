<template>
  <form @submit.prevent="handleSubmit" class="add-form">
    <h2>Добавить новую книгу</h2>
    <input v-model="formData.title" type="text" placeholder="Название книги" required/>
    <input v-model="formData.author" type="text" placeholder="Автор" required/>
    <select v-model="formData.genre" required>
      <option value="">Выберите жанр</option>
      <option value="Роман">Роман</option>
      <option value="Фантастика">Фантастика</option>
      <option value="Детектив">Детектив</option>
      <option value="Научная">Научная</option>
      <option value="Поэзия">Поэзия</option>
    </select>
    <button type="submit" class="btn-submit">Добавить книгу</button>
  </form>
</template>

<script setup>
import { reactive } from 'vue'
const emit = defineEmits(['add-book'])
const formData = reactive({ title:'', author:'', genre:'' })

const handleSubmit = () => {
  emit('add-book', { ...formData })
  formData.title = ''; formData.author = ''; formData.genre = ''
}
</script>

<style scoped>
.add-form { background:white; padding:20px; border-radius:8px; margin-bottom:20px; }
input, select { width:100%; padding:10px; margin-bottom:10px; border:1px solid #ddd; border-radius:4px; }
.btn-submit { width:100%; padding:12px; background:#4CAF50; color:white; border:none; border-radius:4px; cursor:pointer; }
.btn-submit:hover { background:#45a049; }
</style>