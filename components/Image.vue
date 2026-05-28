<script setup>
import { ref, computed } from 'vue'

const props = defineProps({
  src: { type: String, required: true },
  width: { type: [String, Number], default: 'auto' },
  height: { type: [String, Number], default: 'auto' },
  rounded: { type: String, default: 'none' }, 
  shadow: { type: String, default: 'shadow-xl' },
  zoom: { type: Boolean, default: false },
  over: { type: [Boolean, String], default: false } // Accepts 'tl', 'tc', 'tr', 'cl', 'cc', 'cr', 'bl', 'bc', 'br'
})

const isZoomed = ref(false)
const toggleZoom = () => { if (props.zoom) isZoomed.value = !isZoomed.value }
const formatSize = (val) => isNaN(val) ? val : `${val}px`

// Mapping 9 positions to Flexbox alignment classes
const positionMap = {
  tl: 'items-start justify-start',    tc: 'items-start justify-center',  tr: 'items-start justify-end',
  cl: 'items-center justify-start',  cc: 'items-center justify-center', cr: 'items-center justify-end',
  bl: 'items-end justify-start',    bc: 'items-end justify-center',    br: 'items-end justify-end',
}

const alignmentClasses = computed(() => {
  if (typeof props.over === 'string' && positionMap[props.over]) {
    return positionMap[props.over]
  }
  return positionMap.cc // Default to center-center if just 'over' is passed
})
</script>

<template>
  <div v-if="isZoomed" class="fixed inset-0 z-100 bg-black/95 flex items-center justify-center cursor-zoom-out p-10" @click="isZoomed = false">
    <img :src="src" class="max-w-full max-h-full object-contain shadow-2xl" />
  </div>

  <figure class="relative flex flex-col items-center my-4 group select-none" :class="zoom ? 'cursor-zoom-in' : ''" @click="toggleZoom">
    <div :class="[shadow, `rounded-${rounded}`]" class="relative overflow-hidden transition-all duration-300 group-hover:scale-[1.02]">
      <img :src="src" :style="{ width: formatSize(width), height: formatSize(height) }" class="block object-cover" />

      <div v-if="over && $slots.default" :class="alignmentClasses" class="absolute inset-0 flex p-0 pointer-events-none">
        <div class="px-1 py-0 backdrop-blur-sm rounded-none bg-white/10 dark:bg-black/20 border border-white/10 text-current text-xs tracking-widest shadow-sm">
          <slot />
        </div>
      </div>
    </div>
    
    <figcaption v-if="!over && $slots.default" class="mt-0 text-sm opacity-60 text-center leading-snug max-w-sm">
      <slot />
    </figcaption>
  </figure>
</template>