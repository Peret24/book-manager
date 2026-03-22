<template>
  <div class="app">
    <header>
      <h1>Менеджер книг</h1>
    </header>

    <AddBookForm @add-book="addBook"/>
    <BookFilters v-model:searchQuery="searchQuery" v-model:filter="currentFilter" :books="books"/>
    
    <div v-if="filteredBooks.length === 0">Книги не найдены</div>
    
    <div v-else>
      <BookCard v-for="book in filteredBooks" :key="book.id" :book="book"
        @toggle="toggleBook(book.id)" @delete="deleteBook(book.id)" @rate="rateBook(book.id,$event)"/>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, watch } from 'vue'
import AddBookForm from './components/AddBookForm.vue'
import BookFilters from './components/BookFilters.vue'
import BookCard from './components/BookCard.vue'

const books = ref(JSON.parse(localStorage.getItem('books')||'[]'))
const currentFilter = ref('all')
const searchQuery = ref('')

watch(books,newBooks=>{ localStorage.setItem('books',JSON.stringify(newBooks)) },{deep:true})

const addBook = (data) => books.value.push({ id:Date.now(), ...data, completed:false, rating:0 })
const toggleBook = (id) => { const b=books.value.find(x=>x.id===id); if(b){b.completed=!b.completed;b.rating=b.completed?b.rating:0} }
const rateBook = (id,r)=>{ const b=books.value.find(x=>x.id===id); if(b&&b.completed)b.rating=r }
const deleteBook = (id)=>{ if(confirm('Удалить книгу?')) books.value=books.value.filter(x=>x.id!==id) }

const filteredBooks = computed(()=>books.value.filter(b=>{
  if(currentFilter.value==='unread') return !b.completed
  if(currentFilter.value==='read') return b.completed
  return true
}).filter(b=>!searchQuery.value||b.title.toLowerCase().includes(searchQuery.value.toLowerCase())||b.author.toLowerCase().includes(searchQuery.value.toLowerCase())))
</script>