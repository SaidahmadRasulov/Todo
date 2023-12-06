<template>
  <div>
    <div class="container">
      <div class="row py-5">
        <div class="col-12 col-md-4">
          <div class="card">
            <div class="card-header" v-if="editTodo === null">
              <h1>Add Todo</h1>
            </div>
            <div class="card-header" v-else>
              <h1>Edit Todo</h1>
            </div>
            <div class="card-body">
              <form action="#" class="form">
                <div class="mb-3">
                  <label for="title" class="form-label">Title</label>
                  <input type="text" class="form-control" id="title" v-model="title">
                </div>
                <div class="mb-3">
                  <input type="checkbox" class="form-check-control" id="complete" v-model="check">
                  <label for="complete" class="form-label">Completed</label>
                </div>
              </form>
            </div>
            <div class="card-footer text-end" v-if="editTodo === null">
              <button class="btn btn-success" @click="handleAdd">Add</button>
            </div> 
            <div class="card-footer text-end" v-else>
              <button class="btn btn-secondary" @click="handleAddEdit">Edit</button>
            </div>
          </div>
        </div>
        <div class="col-12 col-md-8">
          <table class="table">
            <thead>
              <tr>
                <th>#</th>
                <th>Title</th>
                <th>Complete</th>
                <th>Action</th>
                <th>
                  <select class="form-select" v-model="select" @change="handleSet">
                    <option value="all">All</option>
                    <option value="com">Completed</option>
                    <option value="not">Not Completed</option>
                  </select>
                </th>
                <th>
                  <input type="search" class="form-control" placeholder="Search" v-model="searchQuery">
                </th>
              </tr>
            </thead>
            <tbody v-for="(todo, index) in filteredTodos" v-if="todos.length > 0">
              <tr :key="index + 1" v-if="todo.completed">
                <td>{{ index + 1 }}</td>
                <td class="text-decoration-line-through">{{ todo.title }}</td>
                <td>
                  <button class="btn btn-success btn-sm" @click="handleChange(todo)">Completed</button>
                </td>
                <td class="btn-group">
                  <button class="btn btn-danger" @click="handleDelete(todo.id)">
                    <i class="bi bi-trash"></i>
                  </button>
                  <button class="btn btn-secondary" @click="handleEdit(todo)">
                    <i class="bi bi-pen"></i>
                  </button>
                </td>
              </tr>
              <tr v-else>
                <td>{{ index + 1 }}</td>
                <td>{{ todo.title }}</td>
                <td>
                  <button class="btn btn-danger btn-sm" @click="handleChange(todo)">Not Completed</button>
                </td>
                <td class="btn-group">
                  <button class="btn btn-danger" @click="handleDelete(todo.id)">
                    <i class="bi bi-trash"></i>
                  </button>
                  <button class="btn btn-secondary" @click="handleEdit(todo)">
                    <i class="bi bi-pen"></i>
                  </button>
                </td>
              </tr>
            </tbody>
            <tbody v-else class="text-center">
              <h3 class="w-100 text-center">Not Data</h3>
            </tbody>
          </table>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
import 'bootstrap/dist/css/bootstrap.min.css';
import 'bootstrap-icons/font/bootstrap-icons.css'

export default {
  data() {
    return {
      todos: [],
      select: 'All',
      title: '',
      check: false,
      searchQuery: '',
      editTodo: null,
      updatedTodo: null
    }
  },
  methods: {
    handleChange(item) {
      item.completed = !item.completed;
      console.log(item)
    },
    handleSet(sort) {
      this.select = sort.target.value;
      console.log(this.select)
    },
    handleAdd() {
      let newTodo = {
        id: Date.now(),
        title: this.title,
        completed: this.check
      };
      if (this.title !== '') {
        this.todos.push(newTodo);
        this.title = '';
        this.check = false
      } else {
        alert('Fill all the gaps')
      }
    },
    handleDelete(id) {
      this.todos = this.todos.filter(todo => todo.id !== id)
    },
    handleEdit(item) {
      this.editTodo = item;
      this.title = this.editTodo.title,
      this.check = this.editTodo.completed
      if(this.editTodo.title == item.title && this.editTodo.completed == item.completed) {
        
      }
    },
    handleAddEdit() {
      this.todos.map((item) => {
        if(item.title == this.editTodo.title && item.completed == this.editTodo.completed) {
          this.updatedTodo = this.editTodo;
          this.updatedTodo.title = this.title;
          this.updatedTodo.completed = this.check;
          console.log(this.updatedTodo);
          this.title = '';
          this.check = false;
          this.editTodo = null
        }
      })
    }
  },
  computed: {
    filteredTodos() {
      if (this.select == 'com') {
        return this.todos.filter(todo => {
          return todo.completed == true && todo.title.toLowerCase().includes(this.searchQuery.toLowerCase())
        })
      } else if (this.select == 'not') {
        return this.todos.filter(todo => {
          return !todo.completed && todo.title.toLowerCase().includes(this.searchQuery.toLowerCase())
        })
      } else {
        return this.todos.filter(todo => {
          return todo && todo.title.toLowerCase().includes(this.searchQuery.toLowerCase())
        })
      }
    }
  },
  mounted() {
    fetch('https://jsonplaceholder.typicode.com/todos?_limit=10')
      .then(res => res.json())
      .then(data => {
        this.todos = data
      })
  }
}
</script>
<style lang="">
  
</style>