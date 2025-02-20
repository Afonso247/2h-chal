<template>
  <div class="container mx-auto p-4">
    <div class="flex justify-between items-center mb-6">
      <h2 class="text-2xl font-bold">Meus Livros</h2>
      <button 
        @click="showModal = true"
        class="bg-green-500 text-white px-4 py-2 rounded hover:bg-green-600"
      >
        Adicionar Livro
      </button>
    </div>

    <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4">
      <div 
        v-for="book in books" 
        :key="book.id"
        class="border p-4 rounded shadow"
      >
        <h3 class="text-xl font-semibold mb-2">{{ book.titulo }}</h3>
        <p><strong>Autor:</strong> {{ book.autor }}</p>
        <p><strong>Gêneros:</strong> {{ book.generos.join(', ') }}</p>
        <p><strong>Páginas:</strong> {{ book.paginas }}</p>
        <div class="mt-4 flex gap-2">
          <button 
            @click="editBook(book)"
            class="bg-blue-500 text-white px-3 py-1 rounded hover:bg-blue-600"
          >
            Editar
          </button>
          <button 
            @click="deleteBook(book.id)"
            class="bg-red-500 text-white px-3 py-1 rounded hover:bg-red-600"
          >
            Excluir
          </button>
        </div>
      </div>
    </div>

    <!-- Modal para adicionar/editar livro -->
    <div v-if="showModal" class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center">
      <div class="bg-white p-6 rounded-lg w-full max-w-md">
        <h3 class="text-xl font-bold mb-4">
          {{ editingBook ? 'Editar Livro' : 'Novo Livro' }}
        </h3>
        <form @submit.prevent="saveBook">
          <div class="mb-4">
            <label class="block mb-2">Título:</label>
            <input 
              v-model="bookForm.titulo" 
              type="text" 
              required
              class="w-full p-2 border rounded"
            >
          </div>
          <div class="mb-4">
            <label class="block mb-2">Autor:</label>
            <input 
              v-model="bookForm.autor" 
              type="text" 
              required
              class="w-full p-2 border rounded"
            >
          </div>
          <div class="mb-4">
            <label class="block mb-2">Gêneros (separados por vírgula):</label>
            <input 
              v-model="bookForm.generos" 
              type="text" 
              required
              class="w-full p-2 border rounded"
            >
          </div>
          <div class="mb-4">
            <label class="block mb-2">Páginas:</label>
            <input 
              v-model="bookForm.paginas" 
              type="number" 
              required
              class="w-full p-2 border rounded"
            >
          </div>
          <div class="flex justify-end gap-2">
            <button 
              type="button"
              @click="showModal = false"
              class="bg-gray-500 text-white px-4 py-2 rounded hover:bg-gray-600"
            >
              Cancelar
            </button>
            <button 
              type="submit"
              class="bg-blue-500 text-white px-4 py-2 rounded hover:bg-blue-600"
            >
              Salvar
            </button>
          </div>
        </form>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      books: [],
      showModal: false,
      editingBook: null,
      bookForm: {
        titulo: '',
        autor: '',
        generos: '',
        paginas: ''
      }
    }
  },
  created() {
    this.fetchBooks()
  },
  methods: {
    async fetchBooks() {
      try {
        const response = await fetch('api/books', {
          headers: {
            'Authorization': `Bearer ${localStorage.getItem('token')}`
          }
        })
        if (response.ok) {
          this.books = await response.json()
        }
      } catch (error) {
        console.error('Erro ao buscar livros:', error)
      }
    },
    editBook(book) {
      this.editingBook = book
      this.bookForm = {
        titulo: book.titulo,
        autor: book.autor,
        generos: book.generos.join(', '),
        paginas: book.paginas
      }
      this.showModal = true
    },
    async saveBook() {
      try {
        const bookData = {
          ...this.bookForm,
          generos: this.bookForm.generos.split(',').map(g => g.trim())
        }
        
        const url = this.editingBook 
          ? `api/books/${this.editingBook.id}`
          : 'api/books'
        
        const method = this.editingBook ? 'PUT' : 'POST'
        
        const response = await fetch(url, {
          method,
          headers: {
            'Content-Type': 'application/json',
            'Authorization': `Bearer ${localStorage.getItem('token')}`
          },
          body: JSON.stringify(bookData)
        })

        if (response.ok) {
          this.showModal = false
          this.editingBook = null
          this.resetForm()
          await this.fetchBooks()
        }
      } catch (error) {
        console.error('Erro ao salvar livro:', error)
      }
    },
    async deleteBook(id) {
      if (confirm('Tem certeza que deseja excluir este livro?')) {
        try {
          const response = await fetch(`api/books/${id}`, {
            method: 'DELETE',
            headers: {
              'Authorization': `Bearer ${localStorage.getItem('token')}`
            }
          })
          if (response.ok) {
            await this.fetchBooks()
          }
        } catch (error) {
          console.error('Erro ao excluir livro:', error)
        }
      }
    },
    resetForm() {
      this.bookForm = {
        titulo: '',
        autor: '',
        generos: '',
        paginas: ''
      }
    }
  }
}
</script>