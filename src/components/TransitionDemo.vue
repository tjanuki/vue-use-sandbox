<script setup lang="ts">
import { ref } from 'vue'
import { useTransition, TransitionPresets } from '@vueuse/core'

// Numeric value transitions
const source = ref(0)
const output = useTransition(source, {
  duration: 1000,
  transition: TransitionPresets.easeInOutCubic,
})

// Position values
const positionX = ref(0)
const positionY = ref(0)
const transitionX = useTransition(positionX, {
  duration: 500,
  transition: TransitionPresets.easeOutBack,
})
const transitionY = useTransition(positionY, {
  duration: 500,
  transition: TransitionPresets.easeOutBack,
})

// Scale value
const scale = ref(100)
const transitionScale = useTransition(scale, {
  duration: 300,
  transition: TransitionPresets.easeOutQuad,
})

// Methods
const incrementValue = () => {
  source.value += 25
}

const decrementValue = () => {
  source.value = Math.max(0, source.value - 25)
}

const movePosition = () => {
  positionX.value = positionX.value === 0 ? 200 : 0
  positionY.value = positionY.value === 0 ? 50 : 0
}

const handleHover = (isHovering: boolean) => {
  scale.value = isHovering ? 110 : 100
}
</script>

<template>
  <div class="p-6 max-w-3xl mx-auto space-y-12">
    <!-- Numeric Transition -->
    <div class="bg-white rounded-lg shadow-lg p-6">
      <h2 class="text-xl font-bold text-gray-800 mb-4">Numeric Transition</h2>
      <div class="space-y-4">
        <div class="text-4xl font-bold text-blue-600">
          {{ Math.round(output) }}
        </div>
        <div class="flex space-x-2">
          <button
              @click="decrementValue"
              class="px-4 py-2 bg-blue-500 text-white rounded-lg hover:bg-blue-600"
          >
            Decrease
          </button>
          <button
              @click="incrementValue"
              class="px-4 py-2 bg-blue-500 text-white rounded-lg hover:bg-blue-600"
          >
            Increase
          </button>
        </div>
      </div>
    </div>

    <!-- Position Transition -->
    <div class="bg-white rounded-lg shadow-lg p-6">
      <h2 class="text-xl font-bold text-gray-800 mb-4">Position Transition</h2>
      <div class="relative h-32 bg-gray-100 rounded-lg">
        <div
            class="absolute w-16 h-16 rounded-full bg-blue-500"
            :style="{
            left: `${transitionX}px`,
            top: `${transitionY}px`
          }"
        />
        <button
            @click="movePosition"
            class="absolute bottom-4 left-4 px-4 py-2 bg-blue-500 text-white rounded-lg hover:bg-blue-600"
        >
          Move Ball
        </button>
      </div>
    </div>

    <!-- Scale Transition -->
    <div class="bg-white rounded-lg shadow-lg p-6">
      <h2 class="text-xl font-bold text-gray-800 mb-4">Scale Transition</h2>
      <div
          class="bg-gradient-to-r from-purple-500 to-pink-500 p-6 rounded-lg text-white"
          :style="{ transform: `scale(${transitionScale / 100})` }"
          @mouseenter="handleHover(true)"
          @mouseleave="handleHover(false)"
      >
        <h3 class="text-lg font-bold mb-2">Hover Me!</h3>
        <p>This card smoothly scales on hover using useTransition</p>
      </div>
    </div>
  </div>
</template>