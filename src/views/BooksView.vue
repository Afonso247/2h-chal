<template>
  <div class="main-container">
    <Books />
  </div>
</template>

<script>
import Books from '../components/BooksPage.vue'

export default {
  name: 'BooksView',
  components: {
    Books,
  },
}
</script>

<style scoped></style>
