<template>
  <component 
    :is="block ? 'div' : 'span'"
    class="gradient-wrapper bg-clip-text text-transparent bg-gradient-to-r font-bold transition-all duration-500"
    :class="[gradientClasses, block ? 'w-full' : '']"
  >
    <slot />
  </component>
</template>

<script setup>
import { computed } from 'vue'

const props = defineProps({
  from: { default: 'blue' },
  to: { default: 'red' },
  via: { default: '' },
  block: { type: Boolean, default: false } // 👈 New prop added!
})

const gradientClasses = computed(() => {
  // Explicitly defining all permutations ensures UnoCSS 
  // catches every class during the static build phase.
  const fromColors = {
    blue: 'from-blue-600 dark:from-blue-400',
    red: 'from-red-600 dark:from-red-400',
    yellow: 'from-yellow-500 dark:from-yellow-300',
    green: 'from-green-600 dark:from-green-400',
    pink: 'from-pink-500 dark:from-pink-400',
    purple: 'from-purple-600 dark:from-purple-400'
  }

  const toColors = {
    blue: 'to-blue-600 dark:to-blue-400',
    red: 'to-red-600 dark:to-red-400',
    yellow: 'to-yellow-500 dark:to-yellow-300',
    green: 'to-green-600 dark:to-green-400',
    pink: 'to-pink-500 dark:to-pink-400',
    purple: 'to-purple-600 dark:to-purple-400'
  }

  const viaColors = {
    blue: 'via-blue-500 dark:via-blue-400',
    red: 'via-red-500 dark:via-red-400',
    yellow: 'via-yellow-500 dark:via-yellow-300',
    green: 'via-green-500 dark:via-green-400',
    pink: 'via-pink-500 dark:via-pink-400',
    purple: 'via-purple-500 dark:via-purple-400'
  }

  const fromPart = fromColors[props.from] || fromColors['blue']
  const toPart = toColors[props.to] || toColors['red']
  const viaPart = props.via && viaColors[props.via] ? viaColors[props.via] : ''

  return `${fromPart} ${viaPart} ${toPart}`
})
</script>

<style scoped>
/* Forces nested HTML (like KaTeX math equations) to become 
  transparent so the background gradient shows through.
*/
.gradient-wrapper :deep(*) {
  color: transparent !important;
}

/* KaTeX uses borders to draw the lines for fractions.
  We protect them here so fractions don't disappear.
*/
.gradient-wrapper :deep(.katex-html *) {
  border-color: inherit;
}
</style>