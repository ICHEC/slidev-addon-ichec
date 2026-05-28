<template>
  <div 
    class="my-4 rounded-lg overflow-hidden shadow-xl border transition-colors duration-300"
    :class="[
      width === 'auto' ? 'w-fit' : 'w-full',
      'border-gray-200 bg-gray-50 text-gray-800', 
      'dark:border-white/10 dark:bg-[#1e1e1e] dark:text-gray-100'
    ]"
  >
    <div 
      class="px-4 py-2 flex items-center justify-between border-b"
      :class="'border-gray-200 bg-gray-200/50 dark:border-white/5 dark:bg-[#323232]'"
    >
      <div class="flex gap-2">
        <div class="w-3 h-3 rounded-full bg-[#ff5f56]"></div>
        <div class="w-3 h-3 rounded-full bg-[#ffbd2e]"></div>
        <div class="w-3 h-3 rounded-full bg-[#27c93f]"></div>
      </div>
      
      <div v-if="title" class="text-xs font-mono font-bold tracking-widest uppercase opacity-80">
        {{ title }}
      </div>
      
      <div class="w-10"></div>
    </div>

    <div class="p-4 overflow-x-auto bg-white/50 dark:bg-black/20">
      <slot />
    </div>
  </div>
</template>

<script setup>
defineProps({
  title: String,
  width: { default: 'full' }
})
</script>

<style scoped>
:deep(pre) {
  @apply !m-0 !p-0 !bg-transparent !rounded-none;
}
/* This ensures code text is visible even if the theme changes */
:deep(code) {
  @apply bg-transparent p-0;
}
</style>