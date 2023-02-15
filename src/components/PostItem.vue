<template>
  <div class="card">
    <div><strong class="bold-font">Название</strong>: {{ post.title }}</div>
    <div><strong class="bold-font">Описание</strong>: {{ post.body }}</div>
    <hr>
    <app-button
      class="btn"
      :color="'danger'"
      @click="$emit('remove', post.id)"
    >
      Удалить
    </app-button>
  </div>
</template>

<script>

export default {
  name: "PostItem",
  emits: ['remove'],
  props: {
    post: {
      type: Object,
      required: true,
    },
  },
}
</script>

<style scoped>
.bold-font {
  font-weight: bold;
}
</style>
