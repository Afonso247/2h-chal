<template>
  <div class="container mx-auto p-4">
    <h2 class="text-2xl font-bold mb-4">Registro</h2>
    <form @submit.prevent="handleRegister" class="max-w-md">
      <div class="mb-4">
        <label class="block mb-2">Nome:</label>
        <input v-model="form.name" type="text" required class="w-full p-2 border rounded" />
      </div>
      <div class="mb-4">
        <label class="block mb-2">Email:</label>
        <input v-model="form.email" type="email" required class="w-full p-2 border rounded" />
      </div>
      <div class="mb-4">
        <label class="block mb-2">CPF:</label>
        <input v-model="form.cpf" type="text" required class="w-full p-2 border rounded" />
      </div>
      <div class="mb-4">
        <label class="block mb-2">Senha:</label>
        <input v-model="form.password" type="password" required class="w-full p-2 border rounded" />
      </div>
      <button type="submit" class="bg-blue-500 text-white px-4 py-2 rounded hover:bg-blue-600">
        Registrar
      </button>
      <br>
      <button type="button" @click="$router.push('/login')" class="bg-green-500 text-white px-4 py-2 rounded hover:bg-green-600">Fazer Login</button>
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
      },
    }
  },
  methods: {
    async handleRegister() {
      try {
        const response = await fetch('http://localhost:3000/api/register', {
          method: 'POST',
          headers: {
            'Content-Type': 'application/json',
          },
          body: JSON.stringify(this.form),
        })
        if (response.ok) {
          this.$router.push('/login')
        }
      } catch (error) {
        console.error('Erro no registro:', error)
      }
    },
  },
}
</script>
