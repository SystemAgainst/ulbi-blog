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
  <div class="container">
    <div class="card">
      <div class="pagination">
        <ul class="pagination-menu" v-for="pageNum in totalPages" :key="pageNum">
          <li>
            <a
              href=""
              :class="{ 'current-page': page === pageNum }"
              @click.prevent="changePage(pageNum)"
            >{{ pageNum }}</a>
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script>
import {computed, onMounted, ref, watch, watchEffect} from "vue";
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
    const searchQuery = ref("");

    const page = ref(1);
    const limit = ref(10);
    const totalPages = ref(0);

    const selectOptions = [
      { value: 'title', name: 'По названию' },
      { value: 'body', name: 'По содержанию' },
    ];

    const posts = ref([]);

    const createNewPost = (newPost) => {
      posts.value.push(newPost);
      dialogVisible.value = !dialogVisible.value;
    };

    const changePage = (pageNum) => {
      page.value = pageNum;
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
        const resp = await axios.get(`https://jsonplaceholder.typicode.com/posts`, {
          params: {
            _page: page.value,
            _limit: limit.value,
          },
        });
        // 'x-total-count' getting from Networks (f12) and responses
        totalPages.value = Math.ceil(resp.headers['x-total-count'] / limit.value);
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
      return sortedPosts.value.filter(post => post.title.toLowerCase().includes(searchQuery.value.toLowerCase()));
    });

    watch(page, () => {
      fetchPosts();
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
      totalPages,
      page,
      changePage,
    };
  },
}
</script>

<style scoped>
.pagination {
  display: flex;
  padding: 0 2rem;
  justify-content: space-between;
  align-items: center;
  background-color: #fff;
  box-sizing: border-box;
  border-bottom: 1px solid #eaecef;
}
.pagination-menu {
  list-style: none;
  display: flex;
}
.pagination-menu li {
  margin-right: 0.5rem;
}
.pagination-menu li a {
  color: #2c3e50;
  text-decoration: none;
}
/* current-page ~ pagination-menu li a:hover, .pagination-menu li a.active */
.current-page {
  padding: 8px 16px;
  background-color: #eaecef;
  color: #3eaf7c;
  border-bottom: 2px solid #3eaf7c;
  text-decoration: none;
}
</style>
