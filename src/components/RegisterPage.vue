<template>
  <div class="container mx-auto p-4">
    <h2 class="text-2xl font-bold mb-4">Registro</h2>
    <form @submit.prevent="handleRegister" class="max-w-md">
      <!-- Mensagens de feedback -->
      <div v-if="errorMessage" class="mb-4 p-3 bg-red-100 text-red-700 rounded">
        {{ errorMessage }}
      </div>

      <!-- Campo Nome -->
      <div class="mb-4">
        <label for="name" class="block mb-2">Nome completo:</label>
        <input
          id="name"
          v-model.trim="form.name"
          type="text"
          required
          class="w-full p-2 border rounded focus:ring-2 focus:ring-blue-400"
          :disabled="isSubmitting"
        >
      </div>

      <!-- Campo Email -->
      <div class="mb-4">
        <label for="email" class="block mb-2">E-mail:</label>
        <input
          id="email"
          v-model.trim="form.email"
          type="email"
          required
          class="w-full p-2 border rounded focus:ring-2 focus:ring-blue-400"
          :disabled="isSubmitting"
          @input="validateEmail"
        >
        <span v-if="errors.email" class="text-red-500 text-sm">{{ errors.email }}</span>
      </div>

      <!-- Campo CPF -->
      <div class="mb-4">
        <label for="cpf" class="block mb-2">CPF:</label>
        <input
          id="cpf"
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
        <label for="password" class="block mb-2">Senha:</label>
        <input
          id="password"
          v-model.trim="form.password"
          type="password"
          required
          class="w-full p-2 border rounded focus:ring-2 focus:ring-blue-400"
          :disabled="isSubmitting"
          @input="validatePassword"
          minlength="8"
        >
        <span v-if="errors.password" class="text-red-500 text-sm">{{ errors.password }}</span>
      </div>

      <!-- Campo Confirmar Senha -->
      <div class="mb-4">
        <label for="confirmPassword" class="block mb-2">Confirme sua Senha:</label>
        <input
          id="confirmPassword"
          v-model="form.confirmPassword"
          type="password"
          required
          class="w-full p-2 border rounded focus:ring-2 focus:ring-blue-400"
          :disabled="isSubmitting"
          @input="validateConfirmPassword"
        >
        <span v-if="errors.confirmPassword" class="text-red-500 text-sm">{{ errors.confirmPassword }}</span>
      </div>

      <!-- Botões -->
      <div class="flex items-center gap-2">
        <button
          type="submit"
          class="bg-blue-500 text-white px-4 py-2 rounded hover:bg-blue-600 disabled:opacity-50 transition-colors"
        >
          <span v-if="!isSubmitting">Registrar</span>
          <span v-else>Processando...</span>
        </button>

        <br>

        <button
          type="button"
          @click="$router.push('/login')"
          class="bg-gray-200 text-gray-700 px-4 py-2 rounded hover:bg-gray-300 transition-colors"
        >
          Fazer Login
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
        name: '',
        email: '',
        cpf: '',
        password: '',
        confirmPassword: ''
      },
      errors: {
        email: '',
        cpf: '',
        password: '',
        confirmPassword: ''
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

    validateEmail() {
      const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/
      this.errors.email = emailRegex.test(this.form.email) ? '' : 'E-mail inválido'
    },

    validatePassword() {
      this.errors.password = this.form.password.length >= 8 
        ? '' 
        : 'Mínimo 8 caracteres'
    },

    validateConfirmPassword() {
      this.errors.confirmPassword = 
        this.form.password === this.form.confirmPassword 
          ? '' 
          : 'As senhas não coincidem'
    },

    isFormValid() {
      return (
        this.form.name.length >= 3 &&
        this.form.email.includes('@') &&
        this.form.cpf.replace(/\D/g, '').length === 11 &&
        this.form.password.length >= 8 &&
        !Object.values(this.errors).some(error => error)
      )
    },

    async handleRegister() {
      if (!this.isFormValid()) {
        return this.errorMessage = 'Erro ao cadastrar. Tente novamente.'
      }
      this.isSubmitting = true
      this.errorMessage = ''
      
      try {
        const response = await fetch('http://localhost:3000/api/register', {
          method: 'POST',
          headers: {
            'Content-Type': 'application/json',
          },
          body: JSON.stringify(this.form),
        })

        const data = await response.json()

        if (!response.ok) {
          throw new Error(data.message || 'Erro no cadastro')
        }

        this.$router.push('/login')
        
      } catch (error) {
        console.error('Erro:', error)
        this.errorMessage = error.message || 'Erro ao cadastrar. Tente novamente.'
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