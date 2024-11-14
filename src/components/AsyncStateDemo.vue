<script setup lang="ts">
import { useAsyncState } from '@vueuse/core'
import { ref } from 'vue'

interface User {
  id: number
  name: string
  email: string
  status: 'active' | 'inactive'
}

// Simulate API delay
const delay = (ms: number) => new Promise(resolve => setTimeout(resolve, ms))

// Simulate API calls with different delays and possible errors
const fetchUsers = async () => {
  await delay(1500)

  return [
    { id: 1, name: 'John Doe', email: 'john@example.com', status: 'active' },
    { id: 2, name: 'Jane Smith', email: 'jane@example.com', status: 'active' },
    { id: 3, name: 'Bob Johnson', email: 'bob@example.com', status: 'inactive' },
  ] as User[]
}

const fetchUserWithError = async () => {
  await delay(1500)
  throw new Error('Failed to fetch user data')
}

const fetchSlowData = async () => {
  await delay(3000)
  return { message: 'This took a while!' }
}

// Use useAsyncState for basic data fetching
const { state: users, isLoading, error, execute: refreshUsers } = useAsyncState(
    fetchUsers,
    [],
    {
      immediate: true,
      onError: (e) => console.error('Failed to fetch users:', e),
    }
)

// Example with error handling
const {
  state: errorData,
  isLoading: errorLoading,
  error: errorState
} = useAsyncState(
    fetchUserWithError,
    null,
    {
      immediate: true,
      onError: (e) => console.error('Error example:', e),
    }
)

// Example with delayed loading
const {
  state: slowData,
  isLoading: isSlowLoading,
  execute: refreshSlowData
} = useAsyncState(
    fetchSlowData,
    { message: 'Not loaded yet' },
    {
      immediate: false,
      delay: 1000,
    }
)

// Track number of refreshes
const refreshCount = ref(0)
const handleRefresh = async () => {
  refreshCount.value++
  await refreshUsers()
}
</script>

<template>
  <div class="max-w-4xl mx-auto p-6">
    <!-- Basic Data Fetching Example -->
    <div class="mb-10 p-6 border border-gray-200 rounded-lg shadow-sm">
      <h2 class="text-2xl font-bold mb-4">User List</h2>

      <div class="flex items-center gap-4 mb-4">
        <button
            @click="handleRefresh"
            :disabled="isLoading"
            class="px-4 py-2 bg-blue-500 text-white rounded-md
                 hover:bg-blue-600 disabled:bg-blue-300
                 disabled:cursor-not-allowed transition-colors"
        >
          {{ isLoading ? 'Refreshing...' : 'Refresh Data' }}
        </button>
        <span v-if="refreshCount > 0" class="text-sm text-gray-500">
          Refreshed {{ refreshCount }} times
        </span>
      </div>

      <!-- Loading State -->
      <div
          v-if="isLoading"
          class="p-6 text-center text-gray-500 bg-gray-50 rounded-md"
      >
        Loading users...
      </div>

      <!-- Error State -->
      <div
          v-else-if="error"
          class="p-6 text-center text-red-600 bg-red-50 rounded-md"
      >
        Failed to load users: {{ error.message }}
      </div>

      <!-- Data Display -->
      <div
          v-else
          class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4"
      >
        <div
            v-for="user in users"
            :key="user.id"
            class="p-4 border border-gray-200 rounded-lg bg-white"
        >
          <h3 class="font-semibold mb-1">{{ user.name }}</h3>
          <p class="text-sm text-gray-600 mb-2">{{ user.email }}</p>
          <span
              class="inline-block px-2 py-1 text-xs rounded-full capitalize"
              :class="{
              'bg-green-100 text-green-700': user.status === 'active',
              'bg-red-100 text-red-700': user.status === 'inactive'
            }"
          >
            {{ user.status }}
          </span>
        </div>
      </div>
    </div>

    <!-- Error Handling Example -->
    <div class="mb-10 p-6 border border-gray-200 rounded-lg shadow-sm">
      <h2 class="text-2xl font-bold mb-4">Error Handling Example</h2>

      <div
          v-if="errorLoading"
          class="p-6 text-center text-gray-500 bg-gray-50 rounded-md"
      >
        Loading (will fail)...
      </div>
      <div
          v-else-if="errorState"
          class="p-6 text-center text-red-600 bg-red-50 rounded-md"
      >
        {{ errorState.message }}
      </div>
      <div v-else class="p-4 bg-gray-50 rounded-md">
        {{ errorData }}
      </div>
    </div>

    <!-- Delayed Loading Example -->
    <div class="mb-10 p-6 border border-gray-200 rounded-lg shadow-sm">
      <h2 class="text-2xl font-bold mb-4">Delayed Loading Example</h2>

      <button
          @click="refreshSlowData"
          :disabled="isSlowLoading"
          class="px-4 py-2 bg-blue-500 text-white rounded-md
               hover:bg-blue-600 disabled:bg-blue-300
               disabled:cursor-not-allowed transition-colors"
      >
        {{ isSlowLoading ? 'Loading...' : 'Load Slow Data' }}
      </button>

      <div
          v-if="isSlowLoading"
          class="mt-4 p-6 text-center text-gray-500 bg-gray-50 rounded-md"
      >
        This will take 3 seconds...
      </div>
      <div
          v-else
          class="mt-4 p-4 bg-gray-50 rounded-md"
      >
        {{ slowData.message }}
      </div>
    </div>
  </div>
</template>