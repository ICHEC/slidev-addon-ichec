<template>
  <div class="animation-wrapper">
    <slot :value="currentValue" :isPlaying="isPlaying" />
    
    <div v-if="controls" class="mt-4 flex gap-2 items-center justify-center">
      <button @click="isPlaying = !isPlaying" class="text-xs border px-2 py-1 rounded">
        {{ isPlaying ? 'PAUSE' : 'PLAY' }}
      </button>
      <input type="range" v-model.number="currentValue" :min="min" :max="max" />
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, onUnmounted, watch } from 'vue'

const props = defineProps({
  min: { default: 0 },
  max: { default: 360 },
  step: { default: 1 },
  controls: { type: Boolean, default: true }
})

const currentValue = ref(0)
const isPlaying = ref(true)
let frame = null

const loop = () => {
  if (isPlaying.value) {
    currentValue.value += props.step
    if (currentValue.value > props.max) currentValue.value = props.min
  }
  frame = requestAnimationFrame(loop)
}

onMounted(loop)
onUnmounted(() => cancelAnimationFrame(frame))
</script>