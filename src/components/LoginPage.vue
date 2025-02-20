<template>
  <div class="container mx-auto p-4">
    <h2 class="text-2xl font-bold mb-4">Login</h2>
    <form @submit.prevent="handleLogin" class="max-w-md">
      <div class="mb-4">
        <label class="block mb-2">CPF:</label>
        <input 
          v-model="form.cpf" 
          type="text" 
          required
          class="w-full p-2 border rounded"
        >
      </div>
      <div class="mb-4">
        <label class="block mb-2">Senha:</label>
        <input 
          v-model="form.password" 
          type="password" 
          required
          class="w-full p-2 border rounded"
        >
      </div>
      <button 
        type="submit"
        class="bg-blue-500 text-white px-4 py-2 rounded hover:bg-blue-600"
      >
        Entrar
      </button>
    </form>
  </div>
</template>

<script>
export default {
  data() {
    return {
      form: {
        cpf: '',
        password: ''
      }
    }
  },
  methods: {
    async handleLogin() {
      try {
        const response = await fetch('api/login', {
          method: 'POST',
          headers: {
            'Content-Type': 'application/json'
          },
          body: JSON.stringify(this.form)
        })
        if (response.ok) {
          const data = await response.json()
          localStorage.setItem('token', data.token)
          this.$router.push('/books')
        }
      } catch (error) {
        console.error('Erro no login:', error)
      }
    }
  }
}
</script>