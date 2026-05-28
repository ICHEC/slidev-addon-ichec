<template>
  <div v-if="showControls" class="fixed bottom-4 right-4 opacity-50 hover:opacity-100 transition-opacity">
    <audio ref="audioPlayer" controls :src="audioPath"></audio>
  </div>
</template>

<script setup>
import { ref, onMounted, onUnmounted, computed } from 'vue'

const props = defineProps({
  src: { type: String, required: true }, // e.g., "slide1.mp3"
  showControls: { type: Boolean, default: false },
  autoplay: { type: Boolean, default: true }
})

const audioPlayer = ref(null)
const audioPath = computed(() => `/audio/${props.src}`)

onMounted(() => {
  // Create an internal audio object if we aren't using the visible ref
  const audio = audioPlayer.value || new Audio(audioPath.value)
  
  if (props.autoplay) {
    // Browsers often block autoplay unless there's been a user interaction
    // Since you click to change slides, the interaction requirement is usually met
    audio.play().catch(e => console.warn("Autoplay blocked. Click the slide once.", e))
  }

  // Store it so we can stop it later
  p5AudioInstance.value = audio
})

// Use a ref outside the hooks to track the playing audio
const p5AudioInstance = ref(null)

onUnmounted(() => {
  if (p5AudioInstance.value) {
    p5AudioInstance.value.pause()
    p5AudioInstance.value.currentTime = 0
  }
})
</script>