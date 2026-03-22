<template>
  <div class="app-container">
    <!-- Шапка -->
    <header class="text-center py-5 mb-4 bg-primary text-white rounded shadow-sm">
      <h1 class="display-4">Менеджер книг</h1>
      <p class="lead">Управляй своей библиотекой</p>
    </header>

    <main class="container">
      <!-- Форма добавления -->
      <div class="card mb-4 shadow-sm">
        <div class="card-body">
          <h2 class="card-title h5 mb-3">Добавить новую книгу</h2>
          <form @submit.prevent="addBook" class="row g-3 align-items-end">
            <div class="col-md-6">
              <label for="title" class="form-label visually-hidden">Название книги</label>
              <input 
                v-model="formData.title" 
                id="title"
                type="text" 
                class="form-control" 
                placeholder="Название книги" 
                required 
              />
            </div>
            <div class="col-md-4">
              <label for="author" class="form-label visually-hidden">Автор</label>
              <input 
                v-model="formData.author" 
                id="author"
                type="text" 
                class="form-control" 
                placeholder="Автор" 
                required 
              />
            </div>
            <div class="col-md-2">
              <label for="genre" class="form-label visually-hidden">Жанр</label>
              <select v-model="formData.genre" id="genre" class="form-select" required>
                <option value="" disabled selected>Жанр</option>
                <option value="Роман">Роман</option>
                <option value="Фантастика">Фантастика</option>
                <option value="Детектив">Детектив</option>
                <option value="Научная">Научная</option>
                <option value="Поэзия">Поэзия</option>
              </select>
            </div>
            <div class="col-12 text-end">
              <button type="submit" class="btn btn-success px-4">Добавить книгу</button>
            </div>
          </form>
        </div>
      </div>

      <!-- Фильтры и поиск -->
      <div class="card mb-4 shadow-sm">
        <div class="card-body">
          <div class="row g-3">
            <div class="col-md-8">
              <div class="input-group">
                <span class="input-group-text"><i class="bi bi-search"></i></span>
                <input 
                  v-model="searchQuery" 
                  type="text" 
                  class="form-control" 
                  placeholder="Поиск по названию или автору..." 
                />
              </div>
            </div>
            <div class="col-md-4 d-flex gap-2 justify-content-md-end">
              <button
                v-for="option in filterOptions"
                :key="option.value"
                @click="currentFilter = option.value"
                :class="[
                  'btn',
                  currentFilter === option.value ? 'btn-primary' : 'btn-outline-secondary'
                ]"
              >
                {{ option.label }}
              </button>
            </div>
          </div>
          
          <div class="mt-3 text-muted small">
            Всего: <strong>{{ total }}</strong> | Прочитано: <strong>{{ completed }}</strong> | Осталось: <strong>{{ total - completed }}</strong>
          </div>
        </div>
      </div>

      <!-- Список книг -->
      <div v-if="filteredBooks.length === 0" class="alert alert-info text-center" role="alert">
        <h4 class="alert-heading">Книги не найдены :(</h4>
        <p>Добавьте первую книгу или измените параметры поиска.</p>
      </div>

      <div v-else class="row row-cols-1 row-cols-md-2 row-cols-lg-3 g-4">
        <div 
          v-for="book in filteredBooks" 
          :key="book.id" 
          class="col"
        >
          <div class="card h-100 shadow-sm border-0" :class="{ 'border-success': book.completed }">
            <div class="card-body">
              <h5 class="card-title">{{ book.title }}</h5>
              <p class="card-text text-muted">
                <strong>{{ book.author }}</strong>
              </p>
              <span class="badge bg-secondary">{{ book.genre }}</span>
            </div>
            
            <div class="card-footer bg-transparent border-top-0 pb-3">
              <!-- Рейтинг -->
              <div v-if="book.completed" class="mb-2 rating-stars">
                <span
                  v-for="star in 5"
                  :key="star"
                  @click="rateBook(book.id, star)"
                  class="cursor-pointer me-1"
                  style="font-size: 1.5rem; color: gold;"
                >
                  {{ star <= book.rating ? '★' : '☆' }}
                </span>
              </div>
              
              <div class="d-flex justify-content-between align-items-center mt-2">
                <button
                  @click="toggleBook(book.id)"
                  :class="[
                    'btn',
                    book.completed ? 'btn-info' : 'btn-success'
                  ]"
                  style="font-size: 0.9rem;"
                >
                  {{ book.completed ? 'Прочитано' : 'Отметить прочитанной' }}
                </button>
                
                <button @click="deleteBook(book.id)" class="btn btn-danger btn-sm">
                  ✕
                </button>
              </div>
            </div>
          </div>
        </div>
      </div>
    </main>
    
    <footer class="text-center py-4 text-muted mt-5">
      <small>Практическое занятие 15</small>
    </footer>
  </div>
</template>

<script setup>
import { ref, computed, watch } from 'vue'

// Состояние книг
const books = ref([])

// Загрузка из localStorage
const savedBooks = localStorage.getItem('books')
if (savedBooks) {
  books.value = JSON.parse(savedBooks)
}

// Состояния фильтрации
const currentFilter = ref('all')
const searchQuery = ref('')

// Данные формы
const formData = ref({
  title: '',
  author: '',
  genre: ''
})

// Сохранение изменений
watch(books, (newBooks) => {
  localStorage.setItem('books', JSON.stringify(newBooks))
}, { deep: true })

// Добавление книги
const addBook = () => {
  if (!formData.value.title || !formData.value.author || !formData.value.genre) return
  
  const newBook = {
    id: Date.now(),
    ...formData.value,
    completed: false,
    rating: 0
  }
  books.value.push(newBook)
  
  // Очистка формы
  formData.value.title = ''
  formData.value.author = ''
  formData.value.genre = ''
}

// Переключение статуса
const toggleBook = (id) => {
  const book = books.value.find(b => b.id === id)
  if (book) {
    book.completed = !book.completed
    if (!book.completed) {
      book.rating = 0
    }
  }
}

// Оценка книги
const rateBook = (id, rating) => {
  const book = books.value.find(b => b.id === id)
  if (book && book.completed) {
    book.rating = rating
  }
}

// Удаление книги
const deleteBook = (id) => {
  if (confirm('Удалить книгу?')) {
    books.value = books.value.filter(b => b.id !== id)
  }
}

// Фильтрация и поиск
const filteredBooks = computed(() => {
  return books.value
    .filter(book => {
      if (currentFilter.value === 'unread') return !book.completed
      if (currentFilter.value === 'read') return book.completed
      return true
    })
    .filter(book => {
      if (!searchQuery.value) return true
      const query = searchQuery.value.toLowerCase()
      return book.title.toLowerCase().includes(query) ||
             book.author.toLowerCase().includes(query)
    })
})

// Статистика
const total = computed(() => books.value.length)
const completed = computed(() => books.value.filter(b => b.completed).length)

// Опции фильтров
const filterOptions = [
  { value: 'all', label: 'Все' },
  { value: 'unread', label: 'Непрочитанные' },
  { value: 'read', label: 'Прочитанные' }
]
</script>

<style scoped>
/* Небольшая донастройка для курсора звезд */
.cursor-pointer {
  cursor: pointer;
}

.app-container {
  min-height: 100vh;
  background-color: #f8f9fa; /* Светло-серый фон Bootstrap */
}
</style>