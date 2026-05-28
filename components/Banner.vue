<template>
  <div class="flex w-full my-4" :class="alignClasses">
    <div 
      :class="[
        'transition-all duration-300 shadow-lg flex flex-col', 
        width === 'auto' ? 'w-fit' : 'w-full',
        $attrs.class 
      ]"
    >
      <div 
        v-if="title" 
        :class="['px-4 py-1.5 font-bold text-lg rounded-t-md flex items-center gap-2', headerStyle]"
      >
        <div v-if="icon" :class="icon" class="text-xl" />
        <slot name="icon" v-else />
        
        {{ title }}
      </div>

      <div 
        :class="[
          'px-4 py-3 border-1',
          title ? 'rounded-b-md border-t-0' : 'rounded-md',
          contentStyle
        ]"
      >
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
  icon: String, // Pass strings like 'i-carbon-help' or 'i-mdi-check'
  type: { type: String, default: 'info' },
  width: { type: String, default: 'full' },
  align: { type: String, default: 'left' }
})

const alignClasses = computed(() => ({
  'justify-start': props.align === 'left',
  'justify-center': props.align === 'center',
  'justify-end': props.align === 'right',
}))

const headerStyle = computed(() => {
  const styles = {
    info: 'bg-blue-600 text-white',
    success: 'bg-green-600 text-white',
    warning: 'bg-yellow-500 text-black',
    error: 'bg-red-600 text-white'
  }
  return styles[props.type] || styles.info
})

const contentStyle = computed(() => {
  const styles = {
    info: 'bg-blue-50 border-blue-200 text-blue-900 dark:bg-blue-900/20 dark:text-blue-100',
    success: 'bg-green-50 border-green-200 text-green-900 dark:bg-green-900/20 dark:text-green-100',
    warning: 'bg-yellow-50 border-yellow-200 text-yellow-900 dark:bg-yellow-900/20 dark:text-yellow-100',
    error: 'bg-red-50 border-red-200 text-red-900 dark:bg-red-900/20 dark:text-red-100'
  }
  return styles[props.type] || styles.info
})
</script>