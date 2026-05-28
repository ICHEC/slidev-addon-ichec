<template>
  <div class="custom-list" :class="[markerTypeClass, colorClass]">
    <slot />
  </div>
</template>

<script setup>
import { computed } from 'vue'

const props = defineProps({
  marker: { default: 'dot' }, // 'dot', 'arrow', 'dash', or any symbol like '⚡'
  color: { default: 'black' }
})

// Check if it's a named preset or a custom string
const isPreset = computed(() => ['dot', 'arrow', 'dash', 'check'].includes(props.marker))
const markerTypeClass = computed(() => isPreset.value ? `marker-${props.marker}` : 'marker-custom')

const colorClass = computed(() => {
  const colors = {
    'blue': 'text-blue-600 dark:text-blue-400',
    'green': 'text-green-600 dark:text-green-400',
    'red': 'text-red-600 dark:text-red-400',
    'black': 'text-gray-900 dark:text-gray-100'
  }
  return colors[props.color] || colors.black
})
</script>

<style scoped>
.custom-list :deep(ul) { 
  @apply list-none p-0 ml-0 !important; 
  position: relative;
}

.custom-list :deep(li) { 
  @apply relative mb-0 !important; 
}

/* Preset Markers */
.marker-dot :deep(li::before) { content: "•"; @apply absolute -left-4 font-bold; }
.marker-arrow :deep(li::before) { content: "→"; @apply absolute -left-5 font-bold; }
.marker-dash :deep(li::before) { content: "–"; @apply absolute -left-4 font-bold; }
.marker-check :deep(li::before) { content: "✓"; @apply absolute -left-5 font-bold; }

/* Custom Symbol Logic */
.marker-custom :deep(li::before) { 
  content: v-bind('`"${marker}"`'); 
  @apply absolute -left-6 font-bold text-center w-6 h-6 flex items-center justify-center; 
}
</style>