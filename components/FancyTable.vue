<template>
  <div class="flex w-full my-4" :class="alignClasses">
    <div 
      class="fancy-table-wrapper overflow-hidden rounded-lg border border-gray-200 shadow-md" 
      :class="[color, direction === 'vertical' ? 'vertical-mode' : 'horizontal-mode', width === 'auto' ? 'w-fit' : 'w-full']"
    >
      <slot />
    </div>
  </div>
</template>

<script setup>
import { computed } from 'vue'

const props = defineProps({
  color: { default: 'blue' }, // blue, green, yellow, red, gray
  direction: { default: 'horizontal' }, // horizontal or vertical
  width: { default: 'full' },
  align: { default: 'left' }
})

const alignClasses = computed(() => ({
  'justify-start': props.align === 'left',
  'justify-center': props.align === 'center',
  'justify-end': props.align === 'right',
}))
</script>

<style scoped>
.fancy-table-wrapper :deep(table) {
  @apply w-full border-collapse text-sm;
}

.fancy-table-wrapper :deep(th), .fancy-table-wrapper :deep(td) {
  @apply p-1 px-3 border-b border-gray-100 dark:border-gray-700;
}

/* --- HORIZONTAL MODE (Colored Top Row) --- */
.horizontal-mode :deep(thead) { @apply text-white font-bold; }
.horizontal-mode.blue :deep(thead) { @apply bg-blue-600; }
.horizontal-mode.green :deep(thead) { @apply bg-green-600; }
.horizontal-mode.red :deep(thead) { @apply bg-red-600; }
.horizontal-mode.yellow :deep(thead) { @apply bg-yellow-500 text-black!; }
.horizontal-mode.gray :deep(thead) {@apply bg-gray-50 text-black!; }

/* --- VERTICAL MODE (Colored First Column) --- */
.vertical-mode :deep(tr td:first-child),
.vertical-mode :deep(tr th:first-child) {
  @apply font-bold text-white w-1/3 border-r border-white/10;
}

.vertical-mode.blue :deep(tr td:first-child) { @apply bg-blue-600; }
.vertical-mode.green :deep(tr td:first-child) { @apply bg-green-600; }
.vertical-mode.red :deep(tr td:first-child) { @apply bg-red-600; }
.vertical-mode.yellow :deep(tr td:first-child) { @apply bg-yellow-500 text-black!; }

.fancy-table-wrapper :deep(tbody tr:nth-child(even)) {
  @apply bg-gray-50/50 dark:bg-white/5;
}
</style>