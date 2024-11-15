<script setup lang="ts">
import { useElementSize } from '@vueuse/core'
import { ref } from 'vue'

const target = ref<HTMLElement | null>(null)
const { width, height } = useElementSize(target)

// Toggle size functionality
const isExpanded = ref(false)
const toggleSize = () => {
  isExpanded.value = !isExpanded.value
}
</script>

<template>
  <div class="p-6 max-w-2xl mx-auto">
    <!-- Size display card -->
    <div class="mb-6 bg-gradient-to-r from-blue-500 to-blue-600 rounded-lg p-4 text-white shadow-lg">
      <h2 class="text-xl font-semibold mb-2">Element Dimensions</h2>
      <div class="grid grid-cols-2 gap-4">
        <div class="bg-white/10 rounded p-3">
          <p class="text-sm opacity-75">Width</p>
          <p class="text-2xl font-bold">{{ Math.round(width) }}px</p>
        </div>
        <div class="bg-white/10 rounded p-3">
          <p class="text-sm opacity-75">Height</p>
          <p class="text-2xl font-bold">{{ Math.round(height) }}px</p>
        </div>
      </div>
    </div>

    <!-- Resizable element -->
    <div
        ref="target"
        :class="[
        'transition-all duration-300 ease-in-out p-6 rounded-lg border-2 border-blue-500',
        'bg-white shadow-lg cursor-pointer hover:shadow-xl',
        isExpanded ? 'h-96' : 'h-48'
      ]"
        @click="toggleSize"
    >
      <div class="flex flex-col h-full items-center justify-center space-y-4">
        <p class="text-blue-600 font-medium">
          {{ isExpanded ? 'Click to Shrink' : 'Click to Expand' }}
        </p>
        <p class="text-gray-500 text-sm text-center">
          This element's size is being tracked by useElementSize
        </p>
      </div>
    </div>

    <!-- Responsive info -->
    <p class="mt-4 text-sm text-gray-500 text-center">
      Try resizing your browser window to see the dimensions update in real-time
    </p>
  </div>
</template>