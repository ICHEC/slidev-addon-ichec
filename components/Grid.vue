<template>
  <div 
    class="grid mt-2 mb-2" 
    :class="[gridClass, gapClass, alignClass]"
  >
    <slot />
  </div>
</template>

<script setup>
import { computed } from 'vue'

const props = defineProps({
  cols: { default: '2' }, // '1', '2', '3', '4', '1-2', '2-1', '1-3', '3-1'
  gap:  { default: '4' }, // '2', '4', '8'
  align: { default: 'start' } // 'start', 'center', 'end'
})

const gridClass = computed(() => {
  const layouts = {
    '1':   'grid-cols-1',
    '2':   'grid-cols-2',
    '3':   'grid-cols-3',
    '4':   'grid-cols-4',
    '1-2': 'grid-cols-[1fr_2fr]',
    '2-1': 'grid-cols-[2fr_1fr]',
    '1-3': 'grid-cols-[1fr_3fr]', // 25% | 75%
    '3-1': 'grid-cols-[3fr_1fr]', // 75% | 25%
    '1-4': 'grid-cols-[1fr_4fr]', // 20% | 80%
    '4-1': 'grid-cols-[4fr_1fr]', // 80% | 20%
    '2-3': 'grid-cols-[2fr_3fr]', // 40% | 60%
    '3-2': 'grid-cols-[3fr_2fr]', // 60% | 40%
  }
  return layouts[props.cols] || 'grid-cols-2'
})

const gapClass = computed(() => `gap-${props.gap}`)
const alignClass = computed(() => `items-${props.align}`)
</script>

<style scoped>
/* Ensure nested Markdown elements don't add extra top margin */
:slotted(*) {
  @apply mt-0!;
}
</style>