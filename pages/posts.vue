<template>
  <div class="container">
    <h1>Posts</h1>

    <div class="card-container">
      <b-form-group>
        <b-form-input
          v-model="searchQuery"
          placeholder="Enter a title"
        ></b-form-input>
      </b-form-group>
      <b-list-group v-for="(chunk, index) in paginatedPosts" :key="index">
        <b-list-group-item
          v-for="(post, i) in chunk"
          :key="i"
          class="custom-card"
        >
          <b-card @click="getUser(post.userId)">
            <b-card-title>
              {{ post.title }}
            </b-card-title>
            <b-card-sub-title>
              <b-avatar></b-avatar> {{ post.username }}
            </b-card-sub-title>
            <b-card-text>
              {{ post.body }}
            </b-card-text>
            <b-card-footer> Post ID : {{ post.id }}</b-card-footer>
          </b-card>
          <b-modal v-model="modalShow" title="Author">
            <p class="my-4">
              Post created by the user's name is {{ selectedUser.username }}
            </p>
          </b-modal>
        </b-list-group-item>
      </b-list-group>
    </div>
    <b-pagination
      align="center"
      v-model="currentPage"
      :total-rows="posts.length"
      :per-page="postsPerPage"
      aria-controls="my-table"
    />
  </div>
</template>

<script>
import axios from "axios";
export default {
  async asyncData() {
    let { data: posts } = await axios.get(
      "https://jsonplaceholder.typicode.com/posts"
    );
    let allUsers = await axios.get(
      `https://jsonplaceholder.typicode.com/users/`
    );

    allUsers = allUsers.data;
    posts = posts.map((post) => {
      const user = allUsers.find((user) => user.id === post.userId);
      post.username = user.name;
      return post;
    });
    return { posts };
  },
  computed: {
    paginatedPosts() {
      const start = (this.currentPage - 1) * this.postsPerPage;
      const end = start + this.postsPerPage;
      return this.filteredPosts.slice(start, end).reduce((acc, post, i) => {
        if (i % 2 === 0) acc.push([]);
        acc[acc.length - 1].push(post);
        return acc;
      }, []);
    },
    filteredPosts() {
      if (!this.searchQuery) {
        return this.posts;
      }
      const query = this.searchQuery.toLowerCase();
      return this.posts.filter((post) => {
        return post.title.toLowerCase().includes(query);
      });
    },
  },
  data() {
    return {
      currentPage: 1,
      postsPerPage: 4,
      paginatedPostsData: [],
      searchQuery: "",
      modalShow: false,
      selectedUser: "",
      allUsers: [],
    };
  },
  methods: {
    async getUser(userId) {
      this.modalShow = !this.modalShow;
      const { data: user } = await axios.get(
        `https://jsonplaceholder.typicode.com/users/${userId}`
      );

      this.selectedUser = user;
      this.showUserModal = true;
    },
    showUser({ item }) {
      this.getUser(item.userId);
    },
  },
};
</script>
<style scoped lang="scss">
.container {
  padding: 1rem;
  .card-container {
    display: flex;
    flex-direction: column;
    flex-wrap: nowrap;
    & .list-group {
      & .list-group-item {
        border: none;
        & .card {
          margin: 0.5rem;
          & .card-body {
            & h6 {
              margin: 0.5rem 0 0.5rem 0rem;
            }
          }
        }
      }
    }
  }
}
</style>
