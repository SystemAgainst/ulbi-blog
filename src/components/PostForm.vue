<template>
  <div class="container">
    <form class="card" @submit.prevent="onSubmit" @keydown.enter="onSubmit">
      <h1>Создать новую задачу</h1>

      <div class="form-control">
        <label for="title">Название</label>
        <app-input id="title" v-model="title" />
      </div>

      <div class="form-control">
        <label for="body">Описание</label>
        <textarea id="body" v-model="body"></textarea>
      </div>

      <app-button
          :color="'primary'"
          :disabled="!isValid"
      >
        Создать
      </app-button>
    </form>
  </div>
</template>

<script>
import {computed, ref} from "vue";

export default {
  name: "PostForm",
  props: {
    createPost: {
      type: Function,
      required: true,
    },
  },
  setup(props) {
    const title = ref('');
    const body = ref('');

    const isValid = computed(() => {
      return title.value.trim() !== ''
          && body.value.trim() !== '';
    });

    const initForm = () => {
      title.value = '';
      body.value = '';
    };

    const onSubmit = () => {
      try {
        const newPost = {
          id: Date.now().toString(),
          title: title.value,
          body: body.value,
        };
        initForm();
        props.createPost(newPost);
      } catch (e) {
        console.log(e);
      }
    };

    return {
      isValid,
      title,
      body,
      onSubmit,
    };
  },
}
</script>
