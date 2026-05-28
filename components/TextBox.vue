<template>
  <div 
    class="text-box-container my-0 px-2 py-0 border transition-all duration-300 break-words"
    :class="[
      themeTextClasses,
      roundnessClass,
      shadow ? 'shadow-sm' : '',
      width === 'fit' ? 'inline-block w-fit' : 'block' 
    ]"
    :style="dynamicStyles"
  >
    <div class="flex items-start gap-2 h-full">
      <div v-if="icon" :class="[icon, 'flex-shrink-0 mt-1 text-[12px] opacity-100']" />
      <div class="text-[11px] leading-tight py-0 text-center">
        <slot />
      </div>
    </div>
  </div>
</template>

<script setup>
import { computed } from 'vue'

const props = defineProps({
  color: { default: 'blue' },
  rounded: { default: 'md' }, 
  opacity: { default: 10 }, // Range 0-100
  icon: { default: '' },
  shadow: { type: Boolean, default: false },
  width: { default: 'fit' }
})

// RGB Mapping
const colorMap = {
  blue:   '59, 130, 246',
  green:  '34, 197, 94',
  yellow: '234, 179, 8',
  red:    '239, 68, 68',
  black:  '107, 114, 128'
}

// Text color classes (Tailwind handles these well)
const themeTextClasses = computed(() => {
  const themes = {
    blue:   'text-blue-900 dark:text-blue-100',
    green:  'text-green-900 dark:text-green-100',
    yellow: 'text-yellow-950 dark:text-yellow-50',
    red:    'text-red-900 dark:text-red-100',
    black:  'text-gray-900 dark:text-gray-100'
  }
  return themes[props.color] || themes.blue
})

const dynamicStyles = computed(() => {
  const rgb = colorMap[props.color] || colorMap.blue
  const alpha = props.opacity / 100
  const borderAlpha = Math.min((parseInt(props.opacity) + 30) / 100, 1) // Force border to be much visible

  return {
    width: props.width === 'fit' ? 'fit-content' : props.width,
    '--bg-color': `rgba(${rgb}, ${alpha})`,
    '--br-color': `rgba(${rgb}, ${borderAlpha})`
  }
})

const roundnessClass = computed(() => {
  return {
    none: 'rounded-none',
    sm: 'rounded-sm',
    md: 'rounded-md',
    lg: 'rounded-lg',
    full: 'rounded-full px-4'
  }[props.rounded]
})
</script>

<style scoped>
.text-box-container {
  /* Using !important to force the prop values over any Slidev theme defaults */
  background-color: var(--bg-color) !important;
  border-color: var(--br-color) !important;
}
</style>