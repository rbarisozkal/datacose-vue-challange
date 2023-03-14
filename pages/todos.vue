<template>
  <div class="container">
    <h1>Todos</h1>

    <div class="card-container">
      <b-form-group>
        <b-form-input
          v-model="searchQuery"
          placeholder="Enter a title"
        ></b-form-input>
      </b-form-group>
      <b-list-group v-for="(chunk, index) in paginatedTodos" :key="index">
        <b-list-group-item
          v-for="(todo, i) in chunk"
          :key="i"
          class="custom-card"
        >
          <b-card @click="getUser(todo.userId)">
            <b-card-title>
              {{ todo.title }}
            </b-card-title>
            <b-card-sub-title>
              <b-avatar></b-avatar> {{ todo.username }}
            </b-card-sub-title>
            <b-card-text> Post Id {{ todo.id }} </b-card-text>
            <b-card-footer>
              Todo Status :
              <span v-if="!todo.completed"
                ><BIconXCircleFill variant="danger" />
              </span>
              <span v-else
                ><BIconCheck class="success" variant="success"
              /></span>
            </b-card-footer>
          </b-card>
          <b-modal v-model="modalShow" title="Author">
            <p class="my-4">
              Todo created by the user's name is {{ todo.username }}
            </p>
          </b-modal>
        </b-list-group-item>
      </b-list-group>
    </div>
    <b-pagination
      align="center"
      v-model="currentPage"
      :total-rows="todos.length"
      :per-page="todosPerPage"
      aria-controls="my-table"
    />
  </div>
</template>

<script>
import axios from "axios";
import { BIconCheck, BIconXCircleFill } from "bootstrap-vue";
export default {
  components: {
    BIconCheck,
    BIconXCircleFill,
  },
  async asyncData() {
    let { data: todos } = await axios.get(
      "https://jsonplaceholder.typicode.com/todos"
    );
    let allUsers = await axios.get(
      `https://jsonplaceholder.typicode.com/users/`
    );

    allUsers = allUsers.data;
    todos = todos.map((todo) => {
      const user = allUsers.find((user) => user.id === todo.userId);
      todo.username = user.name;
      return todo;
    });
    return { todos };
  },
  computed: {
    paginatedTodos() {
      const start = (this.currentPage - 1) * this.todosPerPage;
      const end = start + this.todosPerPage;
      return this.filteredTodos.slice(start, end).reduce((acc, todo, i) => {
        if (i % 2 === 0) acc.push([]);
        acc[acc.length - 1].push(todo);
        return acc;
      }, []);
    },
    filteredTodos() {
      if (!this.searchQuery) {
        return this.todos;
      }
      const query = this.searchQuery.toLowerCase();
      return this.todos.filter((todo) => {
        return todo.title.toLowerCase().includes(query);
      });
    },
  },
  data() {
    return {
      currentPage: 1,
      todosPerPage: 4,
      searchQuery: "",
      modalShow: false,
      selectedUser: "",
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
