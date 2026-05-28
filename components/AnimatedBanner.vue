<template>
  <div ref="containerRef" class="my-4 shadow-xl rounded-lg overflow-hidden border border-blue-200 bg-blue-50 dark:bg-blue-900/10 text-blue-900 dark:text-blue-100">
    <div v-if="title" class="px-4 py-2 bg-blue-600 text-white font-bold flex items-center gap-2 title-flash">
      <div v-if="icon" :class="icon" />
      {{ title }}
    </div>

    <div class="p-6 text-xl relative">
      <div ref="sourceText" class="hidden"><slot /></div>
      
      <div ref="displayRef" class="inline-block w-full"></div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, onUnmounted } from 'vue'

const props = defineProps({
  title: String,
  icon: String,
  speed: { default: 30 }
})

const containerRef = ref(null)
const sourceText = ref(null)
const displayRef = ref(null)
const isTyping = ref(false)

let timeoutId = null
let observer = null
let mutationObserver = null
let isIntersecting = false
let wasHiddenByClick = false 

const startTyping = (instant = false) => {
  if (timeoutId) clearTimeout(timeoutId)
  isTyping.value = true
  
  displayRef.value.innerHTML = sourceText.value.innerHTML
  
  const textNodes = []
  const walk = (node) => {
    node.childNodes.forEach(child => {
      if (child.nodeType === Node.TEXT_NODE) {
        textNodes.push({ 
          node: child, 
          text: child.nodeValue, 
          length: child.nodeValue.length 
        })
        child.nodeValue = '' 
      } else if (child.nodeType === Node.ELEMENT_NODE) {
        walk(child)
      }
    })
  }
  walk(displayRef.value)

  // IF INSTANT: Just fill the text immediately and stop.
  if (instant) {
    textNodes.forEach(t => t.node.nodeValue = t.text)
    isTyping.value = false
    return
  }

  // OTHERWISE: Do the slow typing animation
  let currentNodeIndex = 0
  let currentCharIndex = 0

  const type = () => {
    if (!isTyping.value) return

    if (currentNodeIndex < textNodes.length) {
      const currentTarget = textNodes[currentNodeIndex]
      
      currentTarget.node.nodeValue += currentTarget.text.charAt(currentCharIndex)
      currentCharIndex++

      if (currentCharIndex >= currentTarget.length) {
        currentNodeIndex++
        currentCharIndex = 0
        type() 
      } else {
        timeoutId = setTimeout(type, parseInt(props.speed))
      }
    } else {
      isTyping.value = false
    }
  }

  timeoutId = setTimeout(type, 300) 
}

const reset = () => {
  if (timeoutId) clearTimeout(timeoutId)
  if (displayRef.value) displayRef.value.innerHTML = ''
  isTyping.value = false
}

onMounted(() => {
  wasHiddenByClick = containerRef.value?.classList.contains('slidev-vclick-hidden') || false

  // Watcher 1: Slide Transitions
  observer = new IntersectionObserver((entries) => {
    isIntersecting = entries[0].isIntersecting
    
    if (isIntersecting) {
      const classList = Array.from(containerRef.value.classList)
      const hasVClick = classList.some(c => c.includes('slidev-vclick'))
      const isCurrentlyHidden = classList.includes('slidev-vclick-hidden')

      if (hasVClick) {
        if (!isCurrentlyHidden) {
          // It's a v-click element, but it's ALREADY visible on load!
          // This means we navigated backwards. Show instantly, don't animate.
          startTyping(true) 
        }
        // If it is hidden, do nothing. The MutationObserver will catch the click.
      } else {
        // Normal element (no v-click). Type it normally on slide load.
        startTyping(false)
      }
    } else {
      reset() // Clear when leaving slide
    }
  })

  if (containerRef.value) {
    observer.observe(containerRef.value)
  }

  // Watcher 2: Within-Slide Clicks
  mutationObserver = new MutationObserver((mutations) => {
    if (!isIntersecting || !containerRef.value) return 

    mutations.forEach((mutation) => {
      if (mutation.attributeName === 'class') {
        const isCurrentlyHidden = containerRef.value.classList.contains('slidev-vclick-hidden')
        
        if (isCurrentlyHidden !== wasHiddenByClick) {
          if (isCurrentlyHidden) {
            reset() // Hidden by 'back' click
          } else {
            startTyping(false) // Revealed by 'forward' click. Animate!
          }
          wasHiddenByClick = isCurrentlyHidden
        }
      }
    })
  })

  if (containerRef.value) {
    mutationObserver.observe(containerRef.value, { attributes: true, attributeFilter: ['class'] })
  }
})

onUnmounted(() => {
  if (observer) observer.disconnect()
  if (mutationObserver) mutationObserver.disconnect()
  if (timeoutId) clearTimeout(timeoutId)
})
</script>

<style scoped>
.title-flash { animation: flash 1s ease-out forwards; }
@keyframes flash {
  0% { filter: brightness(3); opacity: 0; }
  100% { filter: brightness(1); opacity: 1; }
}

:deep(ul), :deep(ol) {
  margin-top: 0.5rem;
  margin-bottom: 0.5rem;
  padding-left: 1.5rem;
}
:deep(li) {
  margin-bottom: 0.25rem;
}
</style>