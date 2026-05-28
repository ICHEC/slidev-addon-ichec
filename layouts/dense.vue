<template>
  <div class="slidev-layout dense w-full h-full p-6">
    <slot />
  </div>
</template>

<style scoped>
.dense {
  display: flex;
  flex-direction: column;
}

/* Force all headings in this layout to be compact */
:deep(h1) {
  @apply mt-0 mb-2 text-3xl font-bold border-b border-gray-200 pb-1;
}

:deep(h2), :deep(h3) {
  @apply mt-2 mb-1;
}

/* Tighten grid spacing globally for this layout */
:deep(.grid) {
  @apply mt-1 mb-1;
}
</style>