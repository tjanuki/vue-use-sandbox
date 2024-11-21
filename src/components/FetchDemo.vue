<script setup lang="ts">
import { ref } from 'vue'
import { useFetch } from '@vueuse/core'

interface Post {
  id: number
  title: string
  body: string
  userId: number
}

interface User {
  id: number
  name: string
  email: string
}

// Demo API endpoints
const POST_URL = 'https://jsonplaceholder.typicode.com/posts'
const USERS_URL = 'https://jsonplaceholder.typicode.com/users'

// Post ID for fetching specific post
const postId = ref(1)

// Fetch posts with reactive URL
const {
  data: post,
  error: postError,
  isFetching: isLoadingPost,
  execute: refetchPost
} = useFetch<Post>(() => `${POST_URL}/${postId.value}`, {
  immediate: true,
  refetch: true,
  beforeFetch({ url, options }) {
    console.log('Fetching from:', url)
    return { options }
  },
  afterFetch(ctx) {
    // Transform the data if needed
    console.log('Received data:', ctx.data)
    return ctx
  },
}).json()

// Fetch users with abort controller
const abortController = new AbortController()
const {
  data: users,
  error: usersError,
  isFetching: isLoadingUsers,
  abort: abortUsersFetch,
  execute: fetchUsers
} = useFetch<User[]>(USERS_URL, {
  immediate: false,
  signal: abortController.signal,
}).json()

// Error message formatting
const getErrorMessage = (error: any) => {
  if (!error) return null
  return error.message || 'An unknown error occurred'
}
</script>

<template>
  <div class="p-6 max-w-4xl mx-auto">
    <h1 class="text-2xl font-bold mb-8">useFetch Demo</h1>

    <!-- Single Post Section -->
    <div class="mb-8 bg-white rounded-lg shadow p-6">
      <div class="flex items-center gap-4 mb-6">
        <h2 class="text-xl font-semibold">Fetch Single Post</h2>
        <select
            v-model="postId"
            class="px-3 py-2 border rounded"
        >
          <option v-for="i in 10" :key="i" :value="i">
            Post {{ i }}
          </option>
        </select>
        <button
            @click="refetchPost"
            class="px-4 py-2 bg-blue-500 text-white rounded hover:bg-blue-600 disabled:opacity-50"
            :disabled="isLoadingPost"
        >
          Refetch Post
        </button>
      </div>

      <!-- Loading State -->
      <div v-if="isLoadingPost" class="animate-pulse">
        <div class="h-4 bg-gray-200 rounded w-3/4 mb-4"></div>
        <div class="h-4 bg-gray-200 rounded w-1/2 mb-4"></div>
        <div class="h-4 bg-gray-200 rounded w-5/6"></div>
      </div>

      <!-- Error State -->
      <div
          v-else-if="postError"
          class="p-4 bg-red-50 text-red-500 rounded"
      >
        {{ getErrorMessage(postError) }}
      </div>

      <!-- Success State -->
      <div v-else-if="post" class="space-y-4">
        <h3 class="text-lg font-medium">{{ post.title }}</h3>
        <p class="text-gray-600">{{ post.body }}</p>
        <div class="text-sm text-gray-500">
          Post ID: {{ post.id }} | User ID: {{ post.userId }}
        </div>
      </div>
    </div>

    <!-- Users Section -->
    <div class="bg-white rounded-lg shadow p-6">
      <div class="flex items-center gap-4 mb-6">
        <h2 class="text-xl font-semibold">Fetch Users (Cancellable)</h2>
        <button
            @click="fetchUsers"
            class="px-4 py-2 bg-green-500 text-white rounded hover:bg-green-600 disabled:opacity-50"
            :disabled="isLoadingUsers"
        >
          Fetch Users
        </button>
        <button
            @click="abortUsersFetch"
            class="px-4 py-2 bg-red-500 text-white rounded hover:bg-red-600"
            :disabled="!isLoadingUsers"
        >
          Cancel Request
        </button>
      </div>

      <!-- Loading State -->
      <div v-if="isLoadingUsers" class="animate-pulse">
        <div
            v-for="i in 3"
            :key="i"
            class="flex items-center gap-4 mb-4"
        >
          <div class="h-10 w-10 bg-gray-200 rounded-full"></div>
          <div>
            <div class="h-4 bg-gray-200 rounded w-48 mb-2"></div>
            <div class="h-3 bg-gray-200 rounded w-32"></div>
          </div>
        </div>
      </div>

      <!-- Error State -->
      <div
          v-else-if="usersError"
          class="p-4 bg-red-50 text-red-500 rounded"
      >
        {{ getErrorMessage(usersError) }}
      </div>

      <!-- Success State -->
      <div v-else-if="users" class="space-y-4">
        <div
            v-for="user in users"
            :key="user.id"
            class="p-4 border rounded hover:bg-gray-50"
        >
          <div class="font-medium">{{ user.name }}</div>
          <div class="text-sm text-gray-600">{{ user.email }}</div>
        </div>
      </div>

      <!-- Empty State -->
      <div v-else class="text-center text-gray-500 py-8">
        Click "Fetch Users" to load the data
      </div>
    </div>
  </div>
</template>