<template>
  <div class="container">
    <div class="card">
      <h1>Страница с постами</h1>
<!--      <app-button color="" @click="fetchPosts">Получить посты</app-button>-->
      <div class="card-title">
        <div class="form-control">
          <app-input
            v-model="searchQuery"
            placeholder="Поиск..."
          />
        </div>
        <app-button color="" @click="showDialog">Создать пост</app-button>
        <app-select
            v-model="selectedSort"
            :options="selectOptions"
        />
      </div>
    </div>
    <app-dialog v-model:show="dialogVisible">
      <post-form :create-post="createNewPost" />
    </app-dialog>

    <app-loader v-if="loading" />
    <post-list
        v-else
        :posts="sortedAndSearchedPosts"
        @remove="removePost"
    />
  </div>
</template>

<script>
import {computed, onMounted, ref, watch} from "vue";
import PostForm from "@/components/PostForm.vue";
import PostList from "@/components/PostList.vue";
import axios from "axios";

export default {
  name: "App",
  components: { PostList, PostForm },
  setup() {
    const dialogVisible = ref(false);
    const loading = ref(false);
    const selectedSort = ref();
    const searchQuery = ref();

    const selectOptions = [
      { value: 'title', name: 'По названию' },
      { value: 'body', name: 'По содержанию' },
    ];
    // const posts = ref([
    //   { id: 1, title: 'hello 1', body: 'lorem1' },
    //   { id: 2, title: 'hello 2', body: 'lorem2' },
    // ]);
    const posts = ref([]);

    const createNewPost = (newPost) => {
      posts.value.push(newPost);
      dialogVisible.value = !dialogVisible.value;
    };

    const removePost = (id) => {
      posts.value = posts.value.filter((r) => r.id !== id);
    };

    const showDialog = () => {
      dialogVisible.value = !dialogVisible.value;
    };

    const toggleLoadingState = () => {
      loading.value = !loading.value;
    };

    onMounted(() => {
      fetchPosts();
    });

    const fetchPosts = async () => {
      try {
        toggleLoadingState();
        const resp = await axios.get(`https://jsonplaceholder.typicode.com/posts?_limit=10`);
        posts.value = resp.data;
      } catch (e) {
        alert('Ошибка при запросе');
      } finally {
        // по-хорошему, * надо указать в finally, но пока нет, из-за блока setTimeout();
        // как только проверили и обработали все случаи setTimeout() можно убрать
        toggleLoadingState();
      }
    };

    // computed больше подходит, чем watch
    // watch(selectedSort, (newValue) => {
    //   posts.value.sort((post1, post2) => {
    //     return post1[newValue]?.localeCompare(post2[newValue]);
    //     // (prev str equal these str!!!) return post1[selectedSort.value]?.localeCompare(post2[selectedSort.value]);
    //   });
    // });

    const sortedPosts = computed(() => {
      return [...posts.value].sort((post1, post2) => {
        return post1[selectedSort.value]?.localeCompare(post2[selectedSort.value]);
      });
    });

    const sortedAndSearchedPosts = computed(() => {
      return sortedPosts.value.filter(post => post.title.includes(searchQuery.value));
      // filter(post => post.title.includes(searchQuery.value));
    });

    return {
      posts,
      dialogVisible,
      createNewPost,
      removePost,
      showDialog,
      fetchPosts,
      loading,
      selectedSort,
      selectOptions,
      searchQuery,
      sortedPosts,
      sortedAndSearchedPosts,
    };
  },
}
</script>
