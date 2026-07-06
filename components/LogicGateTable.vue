<script setup>
import { ref, computed, onMounted, onUnmounted } from 'vue'

const props = defineProps({
  type: String,
  interval: { type: Number, default: 2000 },
  scale: { type: Number, default: 1 }
})

const currentIndex = ref(0)
let timer = null

// Generate the complete truth table including pre-calculated outputs
const fullTruthTable = computed(() => {
  const baseTable = props.type === 'NOT' 
    ? [{ a: '0', b: '0' }, { a: '1', b: '0' }]
    : [{ a: '0', b: '0' }, { a: '0', b: '1' }, { a: '1', b: '0' }, { a: '1', b: '1' }]

  return baseTable.map(row => {
    const a = row.a === '1'
    const b = row.b === '1'
    let out = '0'
    
    if (props.type === 'AND') out = a && b ? '1' : '0'
    else if (props.type === 'NAND') out = !(a && b) ? '1' : '0'
    else if (props.type === 'XOR') out = a !== b ? '1' : '0'
    else if (props.type === 'NOT') out = !a ? '1' : '0'
    
    return { ...row, out }
  })
})

// Reactive bindings for the animated gate
const currentA = computed(() => fullTruthTable.value[currentIndex.value].a)
const currentB = computed(() => fullTruthTable.value[currentIndex.value].b)
const currentOut = computed(() => fullTruthTable.value[currentIndex.value].out)

// Autonomous timer
onMounted(() => {
  if (props.interval > 0) {
    timer = setInterval(() => {
      currentIndex.value = (currentIndex.value + 1) % fullTruthTable.value.length
    }, props.interval)
  }
})

onUnmounted(() => {
  if (timer) clearInterval(timer)
})
</script>

<template>
  <div class="logic-gate-table-wrapper flex flex-col items-center font-mono"
       :style="{ transform: `scale(${scale})`, transformOrigin: 'top center' }"
       v-motion-slide-visible-right>
    
    <div class="table-container w-full max-w-[250px] mb-8">
      <table class="w-full border-collapse">
        <thead>
          <tr class="bg-gray-400/30 text-lg">
            <th class="p-2 border border-gray-500/40 text-center w-1/3">{{ type === 'NOT' ? 'In' : 'A' }}</th>
            <th v-if="type !== 'NOT'" class="p-2 border border-gray-500/40 text-center w-1/3">B</th>
            <th class="p-2 border border-gray-500/40 text-center w-1/3">Out</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(row, index) in fullTruthTable" :key="index"
              class="transition-colors duration-300 text-lg"
              :class="{'bg-emerald-500/20 text-emerald-400 font-bold shadow-sm': currentIndex === index, 'text-gray-400 opacity-70': currentIndex !== index}">
            <td class="p-2 border border-gray-500/40 text-center">{{ row.a }}</td>
            <td v-if="type !== 'NOT'" class="p-2 border border-gray-500/40 text-center">{{ row.b }}</td>
            <td class="p-2 border border-gray-500/40 text-center" :class="{'text-emerald-500': row.out === '1'}">{{ row.out }}</td>
          </tr>
        </tbody>
      </table>
    </div>

    <div class="gate-container flex items-center relative">
      
      <div class="input-labels flex flex-col justify-around h-[60px] mr-2 text-xl font-bold">
        <div :class="{'text-emerald-500': currentA === '1', 'text-gray-500': currentA !== '1'}">{{ currentA }}</div>
        <div v-if="type !== 'NOT'" :class="{'text-emerald-500': currentB === '1', 'text-gray-500': currentB !== '1'}">{{ currentB }}</div>
      </div>

      <div class="inputs flex flex-col justify-around h-[60px] mr-2">
        <div class="signal-line w-8 h-1 rounded-full" :class="{'bg-emerald-500 shadow-[0_0_8px_#10b981]': currentA === '1', 'bg-gray-500': currentA !== '1'}"></div>
        <div v-if="type !== 'NOT'" class="signal-line w-8 h-1 rounded-full" :class="{'bg-emerald-500 shadow-[0_0_8px_#10b981]': currentB === '1', 'bg-gray-500': currentB !== '1'}"></div>
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

      <div class="output ml-2">
        <div class="signal-line w-10 h-1 rounded-full" :class="{'bg-emerald-500 shadow-[0_0_8px_#10b981]': currentOut === '1', 'bg-gray-500': currentOut !== '1'}"></div>
      </div>
      
      <div class="output-label ml-2 text-xl font-bold" :class="{'text-emerald-500': currentOut === '1', 'text-gray-500': currentOut !== '1'}">
        {{ currentOut }}
      </div>

      <div class="ml-4 text-xl opacity-80 w-16">{{ type }}</div>
    </div>

  </div>
</template>

<style scoped>
.signal-line, .text-emerald-500, .text-gray-500, .bg-emerald-500\/20 {
  transition: all 0.4s ease-in-out;
}
</style>