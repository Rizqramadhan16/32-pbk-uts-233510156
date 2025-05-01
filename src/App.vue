<template>
  <div class="container">
    <!-- Perubahan nama container-->
    <h1>Daftar Kegiatan Hari ini</h1>

    <form @submit.prevent="addTodo">
      <input type="text" v-model="newTodo" placeholder="Tambah kegiatan..." />
      <button type="submit">Tambah</button>
    </form>

    <div class="filters">
      <label class="filter-option">
        <input type="checkbox" v-model="showOnlyPending" />
        Tampilkan hanya yang belum selesai
      </label>

      <button class="clear-completed" @click="clearCompleted" :disabled="!hasCompletedTasks">
        Hapus semua yang selesai
      </button>
    </div>

    <ul>
      <li v-if="filteredTodos.length === 0" class="empty-message">Belum ada kegiatan</li>
      <TodoItem v-for="todo in filteredTodos" :key="todo.id" :todo="todo" @delete="deleteTodo" @toggle="toggleTodo"
        @edit="editTodo" />
    </ul>
  </div>
</template>

<script>
import { ref, computed } from 'vue'
import TodoItem from './components/TodoItem.vue'

export default {
  name: 'App',
  components: {
    TodoItem
  },
  setup() {
    const todos = ref([])
    const newTodo = ref('')
    const showOnlyPending = ref(false)

    const addTodo = () => {
      const text = newTodo.value.trim()
      if (text === '') return

      todos.value.push({
        id: Date.now(),
        text,
        done: false,
        completedAt: null //Tambahkan field waktu selesai
      })

      newTodo.value = ''
    }

    const deleteTodo = (id) => {
      todos.value = todos.value.filter(todo => todo.id !== id)
    }

    const toggleTodo = (id) => {
      const todo = todos.value.find(t => t.id === id)
      if (todo) todo.done = !todo.done
      //Simpan waktu saat ditandai selesai
      todo.completedAt = todo.done ? Date.now() : null
    }

    const editTodo = ({ id, text }) => {
      const todo = todos.value.find(t => t.id === id)
      if (todo) todo.text = text
    }

    const clearCompleted = () => {
      todos.value = todos.value.filter(todo => !todo.done)
    }

    const filteredTodos = computed(() => {
      return showOnlyPending.value
        ? todos.value.filter(todo => !todo.done)
        : todos.value
    })

    const hasCompletedTasks = computed(() => {
      return todos.value.some(todo => todo.done)
    })

     //Format waktu selesai
     const formatDate = (timestamp) => {
      const date = new Date(timestamp)
      return date.toLocaleString()
    }

    return {
      newTodo,
      todos,
      addTodo,
      deleteTodo,
      toggleTodo,
      editTodo,
      clearCompleted,
      showOnlyPending,
      filteredTodos,
      hasCompletedTasks,
      formatDate //return ke template
    }
  }
}
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Poppins:wght@300;400;500;600&display=swap');

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-family: 'Poppins', sans-serif;
}

body {
  background: linear-gradient(135deg, #f5f7fa 0%, #c3cfe2 100%);
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 20px;
}

.container {
  background-color: #fff;
  border-radius: 12px;
  box-shadow: 0 8px 20px rgba(0, 0, 0, 0.1);
  width: 100%;
  max-width: 550px;
  padding: 30px;
}

h1 {
  color: #3a3a3a;
  text-align: center;
  margin-bottom: 25px;
  font-weight: 600;
  font-size: 28px;
}

form {
  display: flex;
  margin-bottom: 20px;
}

input[type="text"] {
  flex: 1;
  padding: 12px 15px;
  border: 1px solid #ddd;
  border-radius: 6px 0 0 6px;
  font-size: 16px;
  outline: none;
  transition: border-color 0.3s;
}

input[type="text"]:focus {
  border-color: #6c5ce7;
}

form button {
  background-color: #6c5ce7;
  color: white;
  border: none;
  padding: 12px 20px;
  border-radius: 0 6px 6px 0;
  cursor: pointer;
  font-size: 16px;
  font-weight: 500;
  transition: background-color 0.3s;
}

form button:hover {
  background-color: #5649c0;
}

.filters {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 20px;
  flex-wrap: wrap;
  gap: 10px;
}

.filter-option {
  display: flex;
  align-items: center;
  font-size: 14px;
  color: #666;
  cursor: pointer;
}

input[type="checkbox"] {
  margin-right: 8px;
  cursor: pointer;
  width: 16px;
  height: 16px;
}

.clear-completed {
  background-color: #ff6b6b;
  color: white;
  border: none;
  border-radius: 4px;
  padding: 8px 12px;
  cursor: pointer;
  font-size: 14px;
  transition: background-color 0.3s;
}

.clear-completed:hover {
  background-color: #ee5253;
}

.clear-completed:disabled {
  background-color: #cccccc;
  cursor: not-allowed;
}

ul {
  list-style-type: none;
  padding: 0;
}

ul li {
  background-color: #f8f9fa;
  border-radius: 8px;
  padding: 15px;
  margin-bottom: 10px;
  transition: transform 0.2s, box-shadow 0.2s;
}

ul li:hover {
  transform: translateY(-2px);
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.05);
}

.todo-content {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.todo-content span {
  flex: 1;
  cursor: pointer;
  color: #333;
  font-size: 16px;
  padding-right: 10px;
}

.action-buttons {
  display: flex;
  gap: 5px;
}

.edit-btn {
  background-color: #3498db;
  color: white;
  border: none;
  border-radius: 4px;
  padding: 6px 12px;
  cursor: pointer;
  font-size: 14px;
  transition: background-color 0.3s;
}

.edit-btn:hover {
  background-color: #2980b9;
}

.delete-btn {
  background-color: #ff6b6b;
  color: white;
  border: none;
  border-radius: 4px;
  padding: 6px 12px;
  cursor: pointer;
  font-size: 14px;
  transition: background-color 0.3s;
}

.delete-btn:hover {
  background-color: #ee5253;
}

.edit-container {
  display: flex;
  flex-direction: column;
  gap: 8px;
}

.edit-container input {
  width: 100%;
  padding: 8px 12px;
  border: 2px solid #6c5ce7;
  border-radius: 4px;
  font-size: 16px;
}

.edit-buttons {
  display: flex;
  justify-content: flex-end;
  gap: 8px;
}

.save-btn {
  background-color: #6c5ce7;
  color: white;
  border: none;
  border-radius: 4px;
  padding: 6px 12px;
  cursor: pointer;
  font-size: 14px;
}

.save-btn:hover {
  background-color: #5649c0;
}

.cancel-btn {
  background-color: #95a5a6;
  color: white;
  border: none;
  border-radius: 4px;
  padding: 6px 12px;
  cursor: pointer;
  font-size: 14px;
}

.cancel-btn:hover {
  background-color: #7f8c8d;
}

ul li span[style*="line-through"] {
  color: #aaa;
  font-style: italic;
}

.empty-message {
  text-align: center;
  color: #95a5a6;
  font-style: italic;
  padding: 20px 0;
}

@keyframes fadeIn {
  from {
    opacity: 0;
    transform: translateY(10px);
  }

  to {
    opacity: 1;
    transform: translateY(0);
  }
}

ul li {
  animation: fadeIn 0.3s ease-out forwards;
}

@media (max-width: 480px) {
  .container {
    padding: 20px 15px;
  }

  h1 {
    font-size: 24px;
  }

  form {
    flex-direction: column;
  }

  input[type="text"] {
    border-radius: 6px;
    margin-bottom: 10px;
  }

  form button {
    border-radius: 6px;
    width: 100%;
  }

  .filters {
    flex-direction: column;
    align-items: flex-start;
  }

  .clear-completed {
    width: 100%;
    text-align: center;
    margin-top: 10px;
  }

  .todo-content {
    flex-direction: column;
    align-items: flex-start;
    gap: 10px;
  }

  .action-buttons {
    width: 100%;
    justify-content: space-between;
  }

  .edit-btn,
  .delete-btn {
    flex: 1;
  }
}
</style>