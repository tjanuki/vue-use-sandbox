<script setup lang="ts">
import { useDark, useToggle } from '@vueuse/core'

const isDark = useDark({
  selector: 'body',
  attribute: 'class',
  valueDark: 'dark',
  valueLight: 'light',
})
const toggleDark = useToggle(isDark)

const features = [
  { title: 'Responsive Design', description: 'Adapts to any screen size' },
  { title: 'Dark Mode', description: 'Easy on the eyes at night' },
  { title: 'Fast Performance', description: 'Optimized for speed' },
  { title: 'Accessibility', description: 'Built with a11y in mind' },
]
</script>

<template>
  <div class="min-h-screen transition-colors duration-300 dark:bg-gray-900">
    <div class="max-w-4xl mx-auto p-6">
      <!-- Header -->
      <header class="flex justify-between items-center mb-12">
        <h1 class="text-2xl font-bold dark:text-white">Dark Mode Demo</h1>
        <button
            @click="toggleDark()"
            class="p-2 rounded-lg bg-gray-200 dark:bg-gray-700 hover:bg-gray-300 dark:hover:bg-gray-600"
        >
          <span class="sr-only">Toggle Dark Mode</span>
          <!-- Sun icon -->
          <svg
              v-if="isDark"
              xmlns="http://www.w3.org/2000/svg"
              class="h-6 w-6 text-yellow-400"
              fill="none"
              viewBox="0 0 24 24"
              stroke="currentColor"
          >
            <path
                strokeLinecap="round"
                strokeLinejoin="round"
                strokeWidth={2}
                d="M12 3v1m0 16v1m9-9h-1M4 12H3m15.364 6.364l-.707-.707M6.343 6.343l-.707-.707m12.728 0l-.707.707M6.343 17.657l-.707.707M16 12a4 4 0 11-8 0 4 4 0 018 0z"
            />
          </svg>
          <!-- Moon icon -->
          <svg
              v-else
              xmlns="http://www.w3.org/2000/svg"
              class="h-6 w-6 text-gray-700"
              fill="none"
              viewBox="0 0 24 24"
              stroke="currentColor"
          >
            <path
                strokeLinecap="round"
                strokeLinejoin="round"
                strokeWidth={2}
                d="M20.354 15.354A9 9 0 018.646 3.646 9.003 9.003 0 0012 21a9.003 9.003 0 008.354-5.646z"
            />
          </svg>
        </button>
      </header>

      <!-- Content -->
      <div class="space-y-8">
        <!-- Hero Section -->
        <div class="p-8 rounded-xl bg-white dark:bg-gray-800 shadow-lg">
          <h2 class="text-xl font-semibold mb-4 dark:text-white">
            Welcome to Dark Mode Demo
          </h2>
          <p class="text-gray-600 dark:text-gray-300">
            This demo shows how to implement dark mode using VueUse's useDark composable.
            Try toggling the theme using the button in the header!
          </p>
        </div>

        <!-- Features Grid -->
        <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
          <div
              v-for="feature in features"
              :key="feature.title"
              class="p-6 rounded-lg bg-white dark:bg-gray-800 shadow-md hover:shadow-lg transition-shadow"
          >
            <h3 class="text-lg font-medium mb-2 dark:text-white">
              {{ feature.title }}
            </h3>
            <p class="text-gray-600 dark:text-gray-300">
              {{ feature.description }}
            </p>
          </div>
        </div>

        <!-- Form Example -->
        <form class="p-6 rounded-xl bg-white dark:bg-gray-800 shadow-lg space-y-4">
          <div>
            <label class="block text-sm font-medium mb-1 dark:text-white">
              Email
            </label>
            <input
                type="email"
                class="w-full px-4 py-2 rounded border dark:bg-gray-700 dark:border-gray-600 dark:text-white"
                placeholder="Enter your email"
            />
          </div>
          <div>
            <label class="block text-sm font-medium mb-1 dark:text-white">
              Message
            </label>
            <textarea
                class="w-full px-4 py-2 rounded border dark:bg-gray-700 dark:border-gray-600 dark:text-white"
                rows="4"
                placeholder="Type your message"
            ></textarea>
          </div>
          <button
              type="submit"
              class="px-4 py-2 bg-blue-500 text-white rounded hover:bg-blue-600 dark:bg-blue-600 dark:hover:bg-blue-700"
          >
            Submit
          </button>
        </form>
      </div>
    </div>
  </div>
</template>

<style>
/* Add to your global CSS */
body.dark {
  color-scheme: dark;
}
</style>