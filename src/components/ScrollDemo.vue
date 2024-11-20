<script setup lang="ts">
import { ref } from 'vue'
import { useScroll, useElementSize } from '@vueuse/core'

// Main container scroll tracking
const mainContainer = ref<HTMLElement | null>(null)
const {
  x: scrollX,
  y: scrollY,
  isScrolling,
  arrivedState: {
    top: isAtTop,
    bottom: isAtBottom,
    left: isAtLeft,
    right: isAtRight
  },
  directions: {
    top: isScrollingUp,
    bottom: isScrollingDown,
    left: isScrollingLeft,
    right: isScrollingRight
  }
} = useScroll(mainContainer)

// Horizontal scroll container
const horizontalContainer = ref<HTMLElement | null>(null)
const {
  x: horizontalScrollX,
  isScrolling: isScrollingHorizontal,
  arrivedState: {
    left: horizontalAtLeft,
    right: horizontalAtRight
  },
  directions: {
    left: isScrollingHorizontalLeft,
    right: isScrollingHorizontalRight
  }
} = useScroll(horizontalContainer)

// Progress bar calculations
const { width } = useElementSize(mainContainer)
const verticalProgress = ref(0)
const horizontalProgress = ref(0)

// Watch scroll position to update progress
const updateProgress = () => {
  if (!mainContainer.value) return

  const { scrollTop, scrollHeight, clientHeight } = mainContainer.value
  verticalProgress.value = (scrollTop / (scrollHeight - clientHeight)) * 100
}

const updateHorizontalProgress = () => {
  if (!horizontalContainer.value) return

  const { scrollLeft, scrollWidth, clientWidth } = horizontalContainer.value
  horizontalProgress.value = (scrollLeft / (scrollWidth - clientWidth)) * 100
}

// Generate content for demo
const items = Array.from({ length: 20 }, (_, i) => ({
  id: i + 1,
  title: `Item ${i + 1}`,
  color: `hsl(${(i * 20) % 360}, 70%, 60%)`
}))

const horizontalItems = Array.from({ length: 15 }, (_, i) => ({
  id: i + 1,
  title: `Card ${i + 1}`,
  color: `hsl(${(i * 25) % 360}, 70%, 60%)`
}))
</script>

<template>
  <div class="h-screen p-6 bg-gray-100">
    <!-- Vertical Scroll Progress Bar -->
    <div
        class="fixed top-0 left-0 h-1 bg-blue-500 transition-all duration-150"
        :style="{ width: `${verticalProgress}%` }"
    />

    <!-- Horizontal Scroll Progress Bar -->
    <div
        class="fixed top-2 left-0 h-1 bg-green-500 transition-all duration-150"
        :style="{ width: `${horizontalProgress}%` }"
    />

    <!-- Scroll Info Panel -->
    <div class="fixed top-4 right-4 bg-white p-4 rounded-lg shadow-lg space-y-2 text-sm z-20">
      <div class="font-bold border-b pb-2 mb-2">Main Container</div>
      <div>Scroll X: {{ Math.round(scrollX) }}px</div>
      <div>Scroll Y: {{ Math.round(scrollY) }}px</div>
      <div>Scrolling: {{ isScrolling ? 'Yes' : 'No' }}</div>
      <div>Vertical: {{ isScrollingDown ? '⬇️' : isScrollingUp ? '⬆️' : '–' }}</div>
      <div class="flex gap-2">
        <span :class="{ 'text-green-500': isAtTop }">Top</span>
        <span :class="{ 'text-green-500': isAtBottom }">Bottom</span>
        <span :class="{ 'text-green-500': isAtLeft }">Left</span>
        <span :class="{ 'text-green-500': isAtRight }">Right</span>
      </div>

      <div class="font-bold border-b pb-2 mb-2 mt-4">Horizontal Container</div>
      <div>Scroll X: {{ Math.round(horizontalScrollX) }}px</div>
      <div>Scrolling: {{ isScrollingHorizontal ? 'Yes' : 'No' }}</div>
      <div>Direction: {{ isScrollingHorizontalRight ? '➡️' : isScrollingHorizontalLeft ? '⬅️' : '–' }}</div>
      <div class="flex gap-2">
        <span :class="{ 'text-green-500': horizontalAtLeft }">Left</span>
        <span :class="{ 'text-green-500': horizontalAtRight }">Right</span>
      </div>
    </div>

    <!-- Main Scrollable Container -->
    <div
        ref="mainContainer"
        @scroll="updateProgress"
        class="h-[80vh] overflow-y-auto rounded-lg bg-white shadow-lg p-6"
    >
      <h2 class="text-2xl font-bold mb-6">Vertical Scroll Demo</h2>

      <!-- Horizontal Scroll Section -->
      <div
          ref="horizontalContainer"
          @scroll="updateHorizontalProgress"
          class="overflow-x-auto whitespace-nowrap mb-8 pb-4"
      >
        <div class="inline-flex gap-4">
          <div
              v-for="item in horizontalItems"
              :key="item.id"
              class="w-64 h-40 rounded-lg flex items-center justify-center text-white font-bold shrink-0"
              :style="{
              backgroundColor: item.color,
              transform: `translateX(${isScrollingHorizontal ? '5px' : '0'})`,
              transition: 'transform 0.3s ease'
            }"
          >
            {{ item.title }}
          </div>
        </div>
      </div>

      <!-- Vertical Scroll Items -->
      <div class="space-y-4">
        <div
            v-for="item in items"
            :key="item.id"
            class="p-6 rounded-lg text-white font-bold"
            :style="{
            backgroundColor: item.color,
            transform: `translateX(${isScrolling ? '10px' : '0'})`,
            transition: 'transform 0.3s ease'
          }"
        >
          {{ item.title }}
        </div>
      </div>

      <!-- Scroll Status Indicators -->
      <div
          class="fixed bottom-4 left-1/2 -translate-x-1/2 bg-black/75 text-white px-4 py-2 rounded-full text-sm flex gap-4"
          :class="{ 'opacity-0': !isScrolling && !isScrollingHorizontal }"
      >
        <span v-if="isScrolling">
          Scrolling {{ isScrollingDown ? 'Down' : 'Up' }}
        </span>
        <span v-if="isScrollingHorizontal">
          Scrolling {{ isScrollingHorizontalRight ? 'Right' : 'Left' }}
        </span>
      </div>
    </div>
  </div>
</template>