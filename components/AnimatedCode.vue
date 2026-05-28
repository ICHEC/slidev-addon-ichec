<template>
  <div 
    class="my-4 rounded-lg overflow-hidden shadow-xl border transition-colors duration-300"
    :class="[
      'border-gray-200 bg-gray-50 text-gray-800', 
      'dark:border-white/10 dark:bg-[#1e1e1e] dark:text-gray-100'
    ]"
  >
    <div class="px-4 py-2 flex items-center justify-between border-b border-gray-200 bg-gray-200/50 dark:border-white/5 dark:bg-[#323232]">
      <div class="flex gap-2">
        <div class="w-3 h-3 rounded-full bg-[#ff5f56]"></div>
        <div class="w-3 h-3 rounded-full bg-[#ffbd2e]"></div>
        <div class="w-3 h-3 rounded-full bg-[#27c93f]"></div>
      </div>
      <div v-if="title" class="text-[10px] font-mono font-bold tracking-widest uppercase opacity-70">
        {{ title }}
      </div>
      <div class="w-10"></div>
    </div>

    <div class="p-5 font-mono text-sm relative whitespace-pre bg-white/50 dark:bg-black/20 overflow-x-auto min-h-12">
      <div ref="source" class="hidden"><slot /></div>
      
      <code class="text-red-700 dark:text-red-300">{{ displayedCode }}</code>
      
      <span 
        v-if="isTyping" 
        class="inline-block w-2 h-4 bg-blue-500 ml-0.5 animate-pulse align-middle"
      >
        {{ cursorType === 'block' ? '|' : '_' }}
      </span>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'

const props = defineProps({
  title: String,
  speed: { default: 30 },
  delay: { default: 500 },
  cursorType: { default: 'block' }
})

const source = ref(null)
const displayedCode = ref('')
const isTyping = ref(true)

onMounted(() => {
  // Use textContent to get only the raw text, ignoring any <span> tags from Shiki
  const fullCode = source.value.textContent.trim()
  let index = 0

  const type = () => {
    if (index < fullCode.length) {
      displayedCode.value += fullCode.charAt(index)
      index++
      setTimeout(type, parseInt(props.speed))
    } else {
      isTyping.value = false
    }
  }

  setTimeout(type, parseInt(props.delay))
})
</script>

<style scoped>
.animate-pulse {
  animation: pulse 0.8s infinite;
}
@keyframes pulse {
  0%, 100% { opacity: 1; }
  50% { opacity: 0; }
}

/* Force-kill any background colors coming from the slot's markdown */
:deep(pre), :deep(code) {
  background-color: transparent !important;
  padding: 0 !important;
  margin: 0 !important;
}
</style>