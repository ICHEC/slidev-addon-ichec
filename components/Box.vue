<template>
  <div class="flex w-full" :class="alignClasses">
    <div 
      :class="[
        'transition-all duration-300 shadow-lg', 
        typeClasses, 
        width === 'auto' ? 'w-fit' : 'w-full',
        $attrs.class 
      ]"
    >
      <div v-if="title" class="group">
        <div class="font-bold text-xl mb-1 flex items-center gap-2">
          <slot name="icon" />
          {{ title }}
        </div>
        <hr class="border-2 opacity-30 mb-3" :class="sepColor" />
      </div>

      <div class="opacity-90">
        <slot />
      </div>
    </div>
  </div>
</template>

<script setup>
import { computed } from 'vue'

defineOptions({ inheritAttrs: false })

const props = defineProps({
  title: String,
  type: { type: String, default: 'info' },
  width: { type: String, default: 'full' },
  align: { type: String, default: 'left' }
})

// Logic to make the separator match the box theme color
const sepColor = computed(() => {
  const colors = {
    info: 'border-blue-900 dark:border-blue-100',
    success: 'border-green-900 dark:border-green-100',
    warning: 'border-yellow-900 dark:border-yellow-100',
    error: 'border-red-900 dark:border-red-100'
  }
  return colors[props.type] || colors.info
})

const alignClasses = computed(() => ({
  'justify-start': props.align === 'left',
  'justify-center': props.align === 'center',
  'justify-end': props.align === 'right',
}))

const typeClasses = computed(() => {
  const styles = {
    // pt-2 reduces top padding, pb-4 keeps room at the bottom
    info: 'bg-blue-50 border-1 border-blue-500 text-blue-900 rounded-md px-4 pt-2 pb-4 my-2',
    success: 'bg-green-50 border-1 border-green-500 text-green-900 rounded-md px-4 pt-2 pb-4 my-2',
    warning: 'bg-yellow-50 border-1 border-yellow-500 text-yellow-900 rounded-md px-4 pt-2 pb-4 my-2',
    error: 'bg-red-50 border-1 border-red-500 text-red-900 rounded-md px-4 pt-2 pb-4 my-2'
  }
  return styles[props.type] || styles.info
})
</script>