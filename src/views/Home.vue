<template>
  <div class="home">
    <Category />
  </div>
</template>

<script>
// @ is an alias to /src
import Category from '@/components/Category.vue'

export default {
  name: 'Home',
  components: {
    Category
  }
}
</script>
