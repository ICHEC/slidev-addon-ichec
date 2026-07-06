<script setup>
import { ref, computed, onMounted, onUnmounted } from 'vue'

const props = defineProps({
  interval: { type: Number, default: 2500 },
  scale: { type: Number, default: 1 }
})

const currentIndex = ref(0)
let timer = null

// Corrected truth table for a Classical Half-Adder
const truthTable = [
  { a: '0', b: '0', sum: '0', carry: '0' },
  { a: '0', b: '1', sum: '1', carry: '0' },
  { a: '1', b: '0', sum: '1', carry: '0' },
  { a: '1', b: '1', sum: '0', carry: '1' }
]

const currentA = computed(() => truthTable[currentIndex.value].a)
const currentB = computed(() => truthTable[currentIndex.value].b)
const currentSum = computed(() => truthTable[currentIndex.value].sum)
const currentCarry = computed(() => truthTable[currentIndex.value].carry)

onMounted(() => {
  if (props.interval > 0) {
    timer = setInterval(() => {
      currentIndex.value = (currentIndex.value + 1) % truthTable.length
    }, props.interval)
  }
})

onUnmounted(() => {
  if (timer) clearInterval(timer)
})
</script>

<template>
  <div class="half-adder-wrapper flex flex-col items-center font-mono"
       :style="{ transform: `scale(${scale})`, transformOrigin: 'top center' }"
       v-motion-slide-visible-right>
    
    <div class="table-container w-full max-w-[350px] mb-10">
      <table class="w-full border-collapse">
        <thead>
          <tr class="bg-gray-800/30 text-lg">
            <th colspan="2" class="p-2 border border-gray-500/40 text-center w-1/2">Input</th>
            <th colspan="2" class="p-2 border border-gray-500/40 text-center text-emerald-500 w-1/2">Output</th>
          </tr>
          <tr class="bg-gray-800/30 text-lg">
            <th class="p-2 border border-gray-500/40 text-center w-1/4">A</th>
            <th class="p-2 border border-gray-500/40 text-center w-1/4">B</th>
            <th class="p-2 border border-gray-500/40 text-center text-emerald-500 w-1/4">Sum</th>
            <th class="p-2 border border-gray-500/40 text-center text-emerald-500 w-1/4">Carry</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(row, index) in truthTable" :key="index"
              class="transition-colors duration-300 text-lg"
              :class="{'bg-emerald-500/20 text-emerald-400 font-bold shadow-sm': currentIndex === index, 'text-gray-400 opacity-70': currentIndex !== index}">
            <td class="p-2 border border-gray-500/40 text-center">{{ row.a }}</td>
            <td class="p-2 border border-gray-500/40 text-center">{{ row.b }}</td>
            <td class="p-2 border border-gray-500/40 text-center" :class="{'text-emerald-500': row.sum === '1'}">{{ row.sum }}</td>
            <td class="p-2 border border-gray-500/40 text-center" :class="{'text-emerald-500': row.carry === '1'}">{{ row.carry }}</td>
          </tr>
        </tbody>
      </table>
    </div>

    <div class="circuit-container flex items-center relative">
      
      <div class="flex flex-col h-[140px] justify-between mr-2 text-xl font-bold py-[10px]">
        <div :class="{'text-emerald-500': currentA === '1', 'text-gray-500': currentA !== '1'}">{{ currentA }}</div>
        <div :class="{'text-emerald-500': currentB === '1', 'text-gray-500': currentB !== '1'}">{{ currentB }}</div>
      </div>

      <svg width="220" height="140" viewBox="0 0 220 140" class="text-current">
        
        <path d="M 10,20 L 110,20" :class="currentA === '1' ? 'wire-active' : 'wire-inactive'" class="wire" />
        <circle cx="40" cy="20" r="4" :class="currentA === '1' ? 'fill-emerald-500' : 'fill-gray-500'" class="transition-colors duration-400" />
        <path d="M 40,20 L 40,100 L 110,100" :class="currentA === '1' ? 'wire-active' : 'wire-inactive'" class="wire" />

        <path d="M 10,120 L 110,120" :class="currentB === '1' ? 'wire-active' : 'wire-inactive'" class="wire" />
        <circle cx="70" cy="120" r="4" :class="currentB === '1' ? 'fill-emerald-500' : 'fill-gray-500'" class="transition-colors duration-400" />
        <path d="M 70,120 L 70,40 L 110,40" :class="currentB === '1' ? 'wire-active' : 'wire-inactive'" class="wire" />

        <g transform="translate(100, 0)" class="gate">
          <path d="M 5,10 Q 15,30 5,50" />
          <path d="M 15,10 Q 25,30 15,50 Q 35,50 50,30 Q 35,10 15,10 Z" />
        </g>

        <g transform="translate(100, 80)" class="gate">
          <path d="M 10,10 L 30,10 A 20,20 0 0 1 50,30 A 20,20 0 0 1 30,50 L 10,50 Z" />
        </g>

        <path d="M 150,30 L 210,30" :class="currentSum === '1' ? 'wire-active' : 'wire-inactive'" class="wire" />
        <path d="M 150,110 L 210,110" :class="currentCarry === '1' ? 'wire-active' : 'wire-inactive'" class="wire" />
      </svg>

      <div class="flex flex-col h-[140px] justify-between ml-2 text-xl font-bold py-[20px]">
        <div class="flex items-center gap-4">
          <span :class="{'text-emerald-500': currentSum === '1', 'text-gray-500': currentSum !== '1'}">{{ currentSum }}</span>
          <span class="opacity-80 font-normal">Sum</span>
        </div>
        <div class="flex items-center gap-4">
          <span :class="{'text-emerald-500': currentCarry === '1', 'text-gray-500': currentCarry !== '1'}">{{ currentCarry }}</span>
          <span class="opacity-80 font-normal">Carry</span>
        </div>
      </div>

    </div>
  </div>
</template>

<style scoped>
.wire {
  transition: all 0.4s ease-in-out;
  stroke-width: 4;
  stroke-linecap: round;
  fill: none;
}
.wire-active {
  stroke: #10b981;
  filter: drop-shadow(0 0 4px #10b981);
}
.wire-inactive {
  stroke: #6b7280;
}
.gate {
  stroke-width: 3;
  stroke: currentColor;
  fill: none;
}
</style>