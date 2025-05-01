<template>
  <li>
    <div v-if="isEditing" class="edit-container">
      <input type="text" v-model="editText" @keyup.enter="saveEdit" ref="editInput" />
      <div class="edit-buttons">
        <button class="save-btn" @click="saveEdit">Simpan</button>
        <button class="cancel-btn" @click="cancelEdit">Batal</button>
      </div>
    </div>
    <div v-else class="todo-content">
      <!-- penambahan check box-->
      <input type="checkbox" :checked="todo.done" @change="$emit('toggle', todo.id)" />
      <span :style="{ textDecoration: todo.done ? 'line-through' : 'none' }" @click="$emit('toggle', todo.id)">
        {{ todo.text }}
      </span>
      <!--Menampilkan waktu selesai jika sudah selesai -->
      <span v-if="todo.done && todo.completedAt" class="completed-time">
        (Selesai: {{ formatTime(todo.completedAt) }})
      </span>

      <div class="action-buttons">
        <button class="edit-btn" @click="startEdit">Edit</button>
        <button class="delete-btn" @click="$emit('delete', todo.id)">Hapus</button>
      </div>
    </div>
  </li>
</template>

<script>
export default {
  name: 'TodoItem',
  props: {
    todo: {
      type: Object,
      required: true
    }
  },
  data() {
    return {
      isEditing: false,
      editText: ''
    }
  },
  methods: {
    startEdit() {
      this.editText = this.todo.text
      this.isEditing = true
      this.$nextTick(() => {
        this.$refs.editInput.focus()
      })
    },
    saveEdit() {
      if (this.editText.trim()) {
        this.$emit('edit', { id: this.todo.id, text: this.editText.trim() })
        this.isEditing = false
      }
    },
    cancelEdit() {
      this.isEditing = false
    },
    //Fungsi untuk memformat waktu penyelesaian
    formatTime(timestamp) {
      const date = new Date(timestamp)
      return date.toLocaleString()
    }
  }
}
</script>
