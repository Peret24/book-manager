<template>
  <div class="book-card" :class="{ completed: book.completed }">
    <div class="book-info">
      <h3>{{ book.title }}</h3>
      <p class="author">{{ book.author }}</p>
      <span class="genre">{{ book.genre }}</span>
    </div>

    <div class="book-actions">
      <div v-if="book.completed" class="rating">
        <span v-for="star in 5" :key="star" @click="$emit('rate', star)">
          {{ star <= book.rating ? '★' : '☆' }}
        </span>
      </div>

      <button @click="$emit('toggle')" :class="['btn', book.completed ? 'btn-secondary' : 'btn-primary']">
        {{ book.completed ? 'Прочитано' : 'Отметить прочитанной' }}
      </button>

      <button @click="$emit('delete')" class="btn btn-danger">✕</button>
    </div>
  </div>
</template>

<script setup>
defineProps(['book'])
defineEmits(['toggle', 'delete', 'rate'])
</script>

<style scoped>
.book-card { background: white; padding:16px; margin-bottom:12px; border-radius:8px; display:flex; justify-content:space-between; }
.book-card.completed { background:#f0f7f0; opacity:0.8; }
.book-info h3 { margin-bottom:4px; }
.rating span { font-size:20px; cursor:pointer; color:gold; }
.btn { padding:8px 12px; border:none; border-radius:4px; cursor:pointer; }
.btn-primary { background:#4CAF50; color:white; }
.btn-secondary { background:#2196F3; color:white; }
.btn-danger { background:#f44336; color:white; }
</style>