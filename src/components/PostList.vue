<template>
  <div class="container">
    <div class="card" v-if="posts.length > 0">
      <h3 class="card-title">{{ title }}</h3>
      <transition-group name="user-list">
        <post-item
            :post="post"
            v-for="post in posts"
            :key="post.id"
            @remove="$emit('remove', post.id)"
        />
      </transition-group>
    </div>
    <h1 class="text-white" v-else>{{ emptyTitle }}</h1>
  </div>
</template>

<script>
import PostItem from "@/components/PostItem.vue";
import {ref} from "vue";

export default {
  name: "PostList",
  emits: ['remove'],
  components: {PostItem},
  props: {
    posts: {
      type: Array,
      required: true,
    },
  },
  setup() {
    const title = ref("Список пользователей");
    const emptyTitle = ref("Список пользователей пуст");

    return {
      title,
      emptyTitle,
    };
  },
}
</script>

<style scoped>
.user-list-item {
  display: inline-block;
  margin-right: 10px;
}
.user-list-enter-active,
.user-list-leave-active {
  transition: all 0.5s ease;
}
.user-list-enter-from,
.user-list-leave-to {
  opacity: 0;
  transform: translateX(-30px);
}
.user-list-move {
  transition: transform 0.8s ease;
}
</style>
