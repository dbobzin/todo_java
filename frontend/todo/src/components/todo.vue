<template>
  <div class="todo-container">
    <h2 class="todo-heading">Todo List</h2>

    <!-- Add Todo Form -->
    <form @submit.prevent="addTodo" class="add-todo-form">
      <input type="text" v-model="newTodo" placeholder="Add a new todo" class="todo-input">
      <button type="submit" class="add-button">+</button>
    </form>

    <!-- Display Todos -->
    <ul class="todo-list">
      <li v-for="todo in todos" :key="todo.id" class="todo-item">
        <input type="checkbox" v-model="todo.completed" @change="updateTodoStatus(todo)" class="todo-checkbox">
        <span :class="{ 'completed': todo.completed }" class="todo-text">{{ todo.title }}</span>
        <button @click="deleteTodo(todo.id)" class="delete-button">Delete</button>
      </li>
    </ul>
  </div>
</template>

<script>
import axios from 'axios';

const data = {
  todos: [],
  newTodo: ''
};

export default {
  data() {
    return {
      todos: [],
      newTodo: ''
    };
  },
  mounted() {
    this.fetchTodos();
  },
  methods: {
    async fetchTodos() {
      try {
        const response = await axios.get('/api/todos');
        this.todos = response.data;
      } catch (error) {
        console.error('Error fetching todos:', error);
      }
    },
    async addTodo() {
      if (this.newTodo.trim() === '') return;
      try {
        const response = await axios.post('/api/todos', {
          title: this.newTodo,
          completed: false
        });
        this.todos.push(response.data);
        this.newTodo = '';
      } catch (error) {
        console.error('Error adding todo:', error);
      }
    },
    async updateTodoStatus(id, todoDetails) {
      try {
        await axios.put(`/api/todos/${id}`, todoDetails);
        // Assuming you want to update the local todos array as well
        const index = this.todos.findIndex(todo => todo.id === id);
        if (index !== -1) {
          this.todos[index] = todoDetails;
        }
      } catch (error) {
        console.error('Error updating todo status:', error);
      }
    },
    async deleteTodo(id) {
      try {
        await axios.delete(`/api/todos/${id}`);
        this.todos = this.todos.filter(todo => todo.id !== id);
      } catch (error) {
        console.error('Error deleting todo:', error);
      }
    }
  }
};
</script>
<style scoped>
.todo-container {
  max-width: 400px;
  margin: 0 auto;
}

.todo-heading {
  text-align: center;
  font-size: 24px;
  margin-bottom: 20px;
}

.add-todo-form {
  display: flex;
  align-items: center;
  margin-bottom: 20px;
}

.todo-input {
  flex: 1;
  padding: 8px;
  font-size: 16px;
  border: 1px solid #ccc;
  border-radius: 4px;
  margin-right: 10px;
}

.add-button {
  padding: 8px 16px;
  font-size: 16px;
  background-color: #4CAF50;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

.add-button:hover {
  background-color: #45a049;
}

.todo-list {
  list-style-type: none;
  padding: 0;
}

.todo-item {
  display: flex;
  align-items: center;
  margin-bottom: 10px;
}

.todo-checkbox {
  margin-right: 10px;
}

.todo-text {
  flex: 1;
  font-size: 16px;
}

.completed {
  text-decoration: line-through;
}

.delete-button {
  padding: 4px 8px;
  font-size: 12px;
  background-color: #f44336;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

.delete-button:hover {
  background-color: #d32f2f;
}
</style>