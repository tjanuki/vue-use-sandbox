<script setup lang="ts">
import { useStorage } from '@vueuse/core'
import { ref, computed } from 'vue'

interface Task {
  id: number
  text: string
  completed: boolean
}

// Initialize persistent storage with default empty array
const tasks = useStorage<Task[]>('my-tasks', [])
const newTaskText = ref('')

// Task management functions
const addTask = () => {
  if (newTaskText.value.trim()) {
    tasks.value.push({
      id: Date.now(),
      text: newTaskText.value,
      completed: false
    })
    newTaskText.value = '' // Clear input after adding
  }
}

const removeTask = (taskId: number) => {
  const index = tasks.value.findIndex(task => task.id === taskId)
  if (index > -1) {
    tasks.value.splice(index, 1)
  }
}

const toggleTask = (taskId: number) => {
  const task = tasks.value.find(task => task.id === taskId)
  if (task) {
    task.completed = !task.completed
  }
}

// Statistics
const stats = computed(() => ({
  total: tasks.value.length,
  completed: tasks.value.filter(task => task.completed).length,
  pending: tasks.value.filter(task => !task.completed).length
}))
</script>

<template>
  <div class="p-4 max-w-lg mx-auto">
    <h2 class="text-2xl font-bold mb-4">Task Manager (with useStorage)</h2>

    <!-- Add new task -->
    <div class="flex gap-2 mb-4">
      <input
          v-model="newTaskText"
          type="text"
          placeholder="Add new task..."
          class="flex-1 px-3 py-2 border rounded"
          @keyup.enter="addTask"
      />
      <button
          @click="addTask"
          class="px-4 py-2 bg-blue-500 text-white rounded hover:bg-blue-600"
      >
        Add
      </button>
    </div>

    <!-- Statistics -->
    <div class="flex gap-4 mb-4 text-sm">
      <span>Total: {{ stats.total }}</span>
      <span class="text-green-600">Completed: {{ stats.completed }}</span>
      <span class="text-blue-600">Pending: {{ stats.pending }}</span>
    </div>

    <!-- Task list -->
    <div class="space-y-2">
      <div
          v-for="task in tasks"
          :key="task.id"
          class="flex items-center gap-2 p-2 border rounded"
      >
        <input
            type="checkbox"
            :checked="task.completed"
            @change="toggleTask(task.id)"
        />
        <span
            :class="{
            'flex-1': true,
            'line-through text-gray-500': task.completed
          }"
        >
          {{ task.text }}
        </span>
        <button
            @click="removeTask(task.id)"
            class="px-2 text-red-500 hover:text-red-700"
        >
          ✕
        </button>
      </div>
    </div>

    <!-- Empty state -->
    <div
        v-if="tasks.length === 0"
        class="text-center text-gray-500 mt-4"
    >
      No tasks yet. Add one above!
    </div>
  </div>
</template>