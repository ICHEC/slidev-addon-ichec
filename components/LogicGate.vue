<script setup>
import { ref, computed, onMounted, onUnmounted, watch } from 'vue'

const props = defineProps({
  type: String,
  interval: { type: Number, default: 0 },
  step: { type: Number, default: 0 },
  gap: { type: Number, default: 0 },    // Gap between lines and gate in pixels
  scale: { type: Number, default: 1 }   // Global scale multiplier (1 = 100%)
})

const currentIndex = ref(props.step)
let timer = null

watch(() => props.step, (newVal) => {
  currentIndex.value = newVal
})

const truthTable = computed(() => {
  if (props.type === 'NOT') return [{ a: '0', b: '0' }, { a: '1', b: '0' }]
  return [{ a: '0', b: '0' }, { a: '0', b: '1' }, { a: '1', b: '0' }, { a: '1', b: '1' }]
})

const currentA = computed(() => truthTable.value[currentIndex.value].a)
const currentB = computed(() => truthTable.value[currentIndex.value].b)

const output = computed(() => {
  const a = currentA.value === '1'
  const b = currentB.value === '1'
  if (props.type === 'AND') return a && b ? '1' : '0'
  if (props.type === 'NAND') return !(a && b) ? '1' : '0'
  if (props.type === 'XOR') return a !== b ? '1' : '0'
  if (props.type === 'NOT') return !a ? '1' : '0'
  return '0'
})

onMounted(() => {
  if (props.interval > 0) {
    timer = setInterval(() => {
      currentIndex.value = (currentIndex.value + 1) % truthTable.value.length
    }, props.interval)
  }
})

onUnmounted(() => {
  if (timer) clearInterval(timer)
})
</script>

<template>
  <div class="gate-container flex items-center relative font-mono" 
       :style="{ transform: `scale(${scale})`, transformOrigin: 'left center' }">
    
    <div class="input-labels flex flex-col justify-around h-[60px] mr-2 text-xl font-bold">
      <div :class="{'text-emerald-500': currentA === '1', 'text-gray-500': currentA !== '1'}">
        {{ currentA }}
      </div>
      <div v-if="type !== 'NOT'" :class="{'text-emerald-500': currentB === '1', 'text-gray-500': currentB !== '1'}">
        {{ currentB }}
      </div>
    </div>

    <div class="inputs flex flex-col justify-around h-[60px]" :style="{ marginRight: `${gap}px` }">
      <div class="signal-line w-8 h-1 rounded-full" 
           :class="{'bg-emerald-500 shadow-[0_0_8px_#10b981]': currentA === '1', 'bg-gray-500': currentA !== '1'}">
      </div>
      <div v-if="type !== 'NOT'" class="signal-line w-8 h-1 rounded-full" 
           :class="{'bg-emerald-500 shadow-[0_0_8px_#10b981]': currentB === '1', 'bg-gray-500': currentB !== '1'}">
      </div>
    </div>

    <div class="gate-symbol text-current">
      <svg v-if="type === 'AND'" width="60" height="60" viewBox="0 0 60 60" fill="none" stroke="currentColor" stroke-width="3" stroke-linecap="round" stroke-linejoin="round">
        <path d="M 10,10 L 30,10 A 20,20 0 0 1 50,30 A 20,20 0 0 1 30,50 L 10,50 Z" />
      </svg>
      <svg v-else-if="type === 'NAND'" width="60" height="60" viewBox="0 0 60 60" fill="none" stroke="currentColor" stroke-width="3" stroke-linecap="round" stroke-linejoin="round">
        <path d="M 5,10 L 25,10 A 20,20 0 0 1 45,30 A 20,20 0 0 1 25,50 L 5,50 Z" />
        <circle cx="51" cy="30" r="6" />
      </svg>
      <svg v-else-if="type === 'NOT'" width="60" height="60" viewBox="0 0 60 60" fill="none" stroke="currentColor" stroke-width="3" stroke-linecap="round" stroke-linejoin="round">
        <path d="M 15,15 L 15,45 L 40,30 Z" />
        <circle cx="46" cy="30" r="6" />
      </svg>
      <svg v-else-if="type === 'XOR'" width="60" height="60" viewBox="0 0 60 60" fill="none" stroke="currentColor" stroke-width="3" stroke-linecap="round" stroke-linejoin="round">
        <path d="M 5,10 Q 15,30 5,50" />
        <path d="M 15,10 Q 25,30 15,50 Q 35,50 50,30 Q 35,10 15,10 Z" />
      </svg>
    </div>

    <div class="output" :style="{ marginLeft: `${gap}px` }">
      <div class="signal-line w-10 h-1 rounded-full" 
           :class="{'bg-emerald-500 shadow-[0_0_8px_#10b981]': output === '1', 'bg-gray-500': output !== '1'}">
      </div>
    </div>
    
    <div class="output-label ml-2 text-xl font-bold"
         :class="{'text-emerald-500': output === '1', 'text-gray-500': output !== '1'}">
      {{ output }}
    </div>

    <div class="ml-4 font-mono text-xl opacity-80 w-16">
      {{ type }}
    </div>

  </div>
</template>

<style scoped>
.signal-line, .text-emerald-500, .text-gray-500 {
  transition: all 0.4s ease-in-out;
}
</style>