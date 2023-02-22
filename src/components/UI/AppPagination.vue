<template>
  <div class="pagination-wrapper">
    <div v-for="page in totalPages" :key="page">{{ page }}</div>
  </div>
</template>

<script>
export default {
  name: "AppPagination",
}
</script>

<style scoped>

</style>
