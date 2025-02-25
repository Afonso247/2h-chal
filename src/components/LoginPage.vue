<template>
  <div class="container mx-auto p-4">
    <h2 class="text-2xl font-bold mb-4">Login</h2>
    <form @submit.prevent="handleLogin" class="max-w-md">
      <!-- Mensagens de feedback -->
      <div v-if="errorMessage" class="mb-4 p-3 bg-red-100 text-red-700 rounded">
        {{ errorMessage }}
      </div>

      <!-- Campo CPF -->
      <div class="mb-4">
        <label for="login-cpf" class="block mb-2">CPF:</label>
        <input
          id="login-cpf"
          v-model="form.cpf"
          type="text"
          required
          class="w-full p-2 border rounded focus:ring-2 focus:ring-blue-400"
          :disabled="isSubmitting"
          maxlength="11"
          placeholder="Somente números"
        >
        <span v-if="errors.cpf" class="text-red-500 text-sm">{{ errors.cpf }}</span>
      </div>

      <!-- Campo Senha -->
      <div class="mb-4">
        <label for="login-password" class="block mb-2">Senha:</label>
        <input
          id="login-password"
          v-model.trim="form.password"
          type="password"
          required
          class="w-full p-2 border rounded focus:ring-2 focus:ring-blue-400"
          :disabled="isSubmitting"
          minlength="8"
        >
      </div>

      <!-- Botões -->
      <div class="flex items-center gap-2">
        <button
          type="submit"
          class="bg-blue-500 text-white px-4 py-2 rounded hover:bg-blue-600 disabled:opacity-50 transition-colors"
        >
          <span v-if="!isSubmitting">Entrar</span>
          <span v-else>Autenticando...</span>
        </button>

        <button
          type="button"
          @click="$router.push('/register')"
          class="bg-gray-200 text-gray-700 px-4 py-2 rounded hover:bg-gray-300 transition-colors"
        >
          Criar nova conta
        </button>
      </div>
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
      },
      errors: {
        cpf: ''
      },
      isSubmitting: false,
      errorMessage: ''
    }
  },
  methods: {

    validateCPF() {
      const cpfDigits = this.form.cpf.replace(/\D/g, '')
      this.errors.cpf = cpfDigits.length === 11 ? '' : 'CPF inválido'
    },

    async handleLogin() {
      this.isSubmitting = true
      this.errorMessage = ''

      try {

        const response = await fetch('http://localhost:3000/api/login', {
          method: 'POST',
          headers: {
            'Content-Type': 'application/json',
          },
          body: JSON.stringify(this.form)
        })

        const data = await response.json()

        if (!response.ok) {
          throw new Error(data.message || 'Credenciais inválidas')
        }

        localStorage.setItem('token', data.token)
        this.$router.push('/books')
        
      } catch (error) {
        console.error('Erro no login:', error)
        this.errorMessage = error.message || 'Erro ao autenticar. Tente novamente.'
      } finally {
        this.isSubmitting = false
      }
    }
  }
}
</script>

<style scoped>
input:disabled {
  @apply bg-gray-100 cursor-not-allowed;
}
</style>