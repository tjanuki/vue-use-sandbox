<script setup lang="ts">
import { useWindowSize, useBreakpoints, breakpointsTailwind } from '@vueuse/core'
import { computed } from 'vue'

// Get reactive window dimensions
const { width, height } = useWindowSize()

// Create breakpoints using Tailwind's default breakpoints
const breakpoints = useBreakpoints(breakpointsTailwind)

// Reactive breakpoint checks
const isMobile = breakpoints.smaller('md')
const isTablet = breakpoints.between('md', 'lg')
const isDesktop = breakpoints.greater('lg')

// Compute dynamic styles based on window size
const boxSize = computed(() => Math.min(width.value, height.value) * 0.3)

// Compute dynamic grid columns based on width
const gridColumns = computed(() => {
  if (width.value < 640) return 1 // sm
  if (width.value < 1024) return 2 // md
  return 3 // lg and above
})

// Sample items for the responsive grid
const items = Array.from({ length: 6 }, (_, i) => ({
  id: i + 1,
  title: `Item ${i + 1}`,
  color: ['bg-blue-200', 'bg-green-200', 'bg-red-200', 'bg-yellow-200', 'bg-purple-200', 'bg-pink-200'][i]
}))
</script>

<template>
  <div class="p-4">
    <!-- Window Size Display -->
    <div class="mb-8 p-4 bg-gray-100 rounded-lg">
      <h2 class="text-xl font-bold mb-4">Window Dimensions</h2>
      <div class="space-y-2">
        <p>Width: {{ width }}px</p>
        <p>Height: {{ height }}px</p>
      </div>
    </div>

    <!-- Responsive Layout Indicator -->
    <div class="mb-8 p-4 bg-blue-100 rounded-lg">
      <h2 class="text-xl font-bold mb-4">Current Breakpoint</h2>
      <div class="space-y-2">
        <p v-if="isMobile" class="text-blue-800">📱 Mobile View (< 768px)</p>
        <p v-if="isTablet" class="text-blue-800">📱 Tablet View (768px - 1024px)</p>
        <p v-if="isDesktop" class="text-blue-800">💻 Desktop View (> 1024px)</p>
      </div>
    </div>

    <!-- Dynamic Size Example -->
    <div class="mb-8">
      <h2 class="text-xl font-bold mb-4">Dynamic Size Example</h2>
      <div
          class="bg-gradient-to-r from-blue-400 to-purple-400 rounded-lg flex items-center justify-center text-white"
          :style="{
          width: `${boxSize}px`,
          height: `${boxSize}px`,
        }"
      >
        <p class="text-center">I resize with the window!</p>
      </div>
    </div>

    <!-- Responsive Grid Example -->
    <div class="mb-8">
      <h2 class="text-xl font-bold mb-4">Responsive Grid ({{ gridColumns }} columns)</h2>
      <div
          class="grid gap-4"
          :style="{
          gridTemplateColumns: `repeat(${gridColumns}, 1fr)`
        }"
      >
        <div
            v-for="item in items"
            :key="item.id"
            :class="[item.color, 'p-4 rounded-lg shadow']"
        >
          <h3 class="font-semibold">{{ item.title }}</h3>
          <p class="text-sm">Grid Item</p>
        </div>
      </div>
    </div>

    <!-- Debug Information -->
    <div class="mt-8 p-4 bg-gray-100 rounded-lg text-sm">
      <h3 class="font-semibold mb-2">Try These:</h3>
      <ul class="list-disc list-inside space-y-1">
        <li>Resize your browser window</li>
        <li>Rotate your device (on mobile)</li>
        <li>Open developer tools and use the responsive design mode</li>
      </ul>
    </div>
  </div>
</template>