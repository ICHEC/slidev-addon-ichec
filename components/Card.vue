<template>
  <div class="flex flex-col overflow-hidden rounded-lg border border-gray-200 bg-white dark:bg-white/5 shadow-md">
    <div v-if="img" class="w-full h-40 overflow-hidden bg-gray-100">
      <img :src="img" class="w-full h-full object-cover" />
    </div>
    
    <div class="p-4 banner-content">
      <div v-if="title" class="font-bold text-lg mb-1 leading-tight">{{ title }}</div>
      <div class="text-sm opacity-80">
        <slot />
      </div>
    </div>
  </div>
</template>

<script setup>
defineProps({
  title: String,
  img: String
})
</script>

<style scoped>
.banner-content :deep(p) { @apply my-0; }
</style>