<script setup lang="ts">
import { ref } from 'vue'
import { useTimeout, useInterval } from '@vueuse/core'

// useTimeout Example
const { ready, start: startTimeout, stop: stopTimeout } = useTimeout(2000, {
  controls: true,
  callback: () => {
    console.log('Timeout completed!')
  }
})

// useInterval Example with controls
const {
  counter,
  reset: resetInterval,
  pause: pauseInterval,
  resume: resumeInterval
} = useInterval(1000, {
  controls: true,
  immediate: true,
  callback: (count) => {
    console.log(`Interval fired: ${count}`)
  }
})

// UI State
const isPaused = ref(false)

const toggleInterval = () => {
  if (isPaused.value) {
    resumeInterval()
  } else {
    pauseInterval()
  }
  isPaused.value = !isPaused.value
}
</script>

<template>
  <div class="p-6 max-w-2xl mx-auto space-y-8">
    <!-- useTimeout Demo -->
    <div class="bg-white rounded-lg shadow-lg p-6">
      <h2 class="text-2xl font-bold text-gray-800 mb-4">useTimeout Demo</h2>

      <div class="space-y-4">
        <div class="flex items-center space-x-2">
          <span class="font-medium">Status:</span>
          <span
              :class="ready ? 'text-green-600' : 'text-blue-600'"
          >
            {{ ready ? 'Ready!' : 'Waiting...' }}
          </span>
        </div>

        <div class="flex space-x-2">
          <button
              @click="startTimeout"
              class="px-4 py-2 bg-blue-500 text-white rounded-lg hover:bg-blue-600"
          >
            Start Timeout
          </button>
          <button
              @click="stopTimeout"
              class="px-4 py-2 bg-red-500 text-white rounded-lg hover:bg-red-600"
          >
            Stop Timeout
          </button>
        </div>
      </div>
    </div>

    <!-- useInterval Demo -->
    <div class="bg-white rounded-lg shadow-lg p-6">
      <h2 class="text-2xl font-bold text-gray-800 mb-4">useInterval Demo</h2>

      <div class="space-y-4">
        <div class="text-4xl font-bold text-blue-600">
          {{ counter }}
        </div>

        <div class="flex space-x-2">
          <button
              @click="toggleInterval"
              class="px-4 py-2 bg-blue-500 text-white rounded-lg hover:bg-blue-600"
          >
            {{ isPaused ? 'Resume' : 'Pause' }}
          </button>
          <button
              @click="resetInterval"
              class="px-4 py-2 bg-gray-500 text-white rounded-lg hover:bg-gray-600"
          >
            Reset
          </button>
        </div>
      </div>
    </div>
  </div>
</template>