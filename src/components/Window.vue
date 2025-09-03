<template>
  <div 
    v-if="!window.minimized"
    class="absolute shadow-2xl overflow-hidden select-none"
    :class="[
      isDragging ? 'window-dragging no-select' : '',
      isResizing ? 'window-resizing no-select' : ''
    ]"
    :style="windowStyle"
    @mousedown="focusWindow"
  >
    <!-- Barra de título -->
    <div 
      class="flex items-center justify-between px-4 py-3 bg-gradient-to-r from-stone-700 to-stone-950 text-white cursor-move"
      @mousedown="startDrag"
    >
      <div class="flex items-center space-x-2">
        <span class="text-sm">📋</span>
        <h3 class="text-sm font-medium">{{ window.title }}</h3>
      </div>
      <div class="flex space-x-2">
        <button class="w-5 h-5 flex items-center justify-center rounded hover:bg-stone-800 transition" @click="minimizeWindow" title="Minimizar">
          <svg width="14" height="14" viewBox="0 0 14 14" fill="none">
        <rect x="3" y="7" width="8" height="2" rx="1" fill="white"/>
          </svg>
        </button>
        
          <button v-show="window.maximized==false" class="w-5 h-5 flex items-center justify-center rounded hover:bg-green-700 transition" @click="maximizeWindow" title="Maximizar">
            <svg width="14" height="14" viewBox="0 0 14 14" fill="none">
              <rect x="3" y="3" width="8" height="8" rx="2" stroke="white" stroke-width="2" fill="none"/>
            </svg>
          </button>
          <button v-show="window.maximized == true" class="w-5 h-5 flex items-center justify-center rounded hover:bg-green-700 transition" @click="decreaseWindow" title="Diminuir">
            <svg width="14" height="14" viewBox="0 0 14 14" fill="none">
              <rect x="3" y="5 " width="8" height="5" rx="2" stroke="white" stroke-width="2" fill="none"/>
            </svg>
          </button>

        <button class="w-5 h-5 flex items-center justify-center rounded hover:bg-red-600 transition" @click="closeWindow" title="Fechar">
          <svg width="14" height="14" viewBox="0 0 14 14" fill="none">
        <line x1="4" y1="4" x2="10" y2="10" stroke="white" stroke-width="2" stroke-linecap="round"/>
        <line x1="10" y1="4" x2="4" y2="10" stroke="white" stroke-width="2" stroke-linecap="round"/>
          </svg>
        </button>
      </div>
    </div>

    <!-- Conteúdo -->
    <div class=" h-[calc(100%-40px)] bg-white overflow-auto">
  <component :is="window.content" v-if="window.content" />
  <div v-else class="text-gray-800 whitespace-pre-line">Sem conteúdo</div>
    </div>

    <!-- Handles para redimensionamento -->
    <div class="absolute top-0 left-0 right-0 h-2 cursor-n-resize" @mousedown="startResize('n')"></div>
    <div class="absolute bottom-0 left-0 right-0 h-2 cursor-s-resize" @mousedown="startResize('s')"></div>
    <div class="absolute top-0 left-0 bottom-0 w-2 cursor-w-resize" @mousedown="startResize('w')"></div>
    <div class="absolute top-0 right-0 bottom-0 w-2 cursor-e-resize" @mousedown="startResize('e')"></div>
    
    <!-- Cantos para redimensionamento -->
    <div class="absolute top-0 left-0 w-3 h-3 cursor-nw-resize" @mousedown="startResize('nw')"></div>
    <div class="absolute top-0 right-0 w-3 h-3 cursor-ne-resize" @mousedown="startResize('ne')"></div>
    <div class="absolute bottom-0 left-0 w-3 h-3 cursor-sw-resize" @mousedown="startResize('sw')"></div>
    <div class="absolute bottom-0 right-0 w-3 h-3 cursor-se-resize" @mousedown="startResize('se')"></div>
  </div>
</template>

<script setup>
import { defineProps, defineEmits, computed, onUnmounted } from 'vue'
import TerminalContent from './TerminalContent.vue'
import BrowserContent from './BrowserContent.vue'
import DocumentsContent from './DocumentsContent.vue'
import SettingsContent from './SettingsContent.vue'

const props = defineProps({
  window: Object
})

const emit = defineEmits(['close', 'focus','decrease','minimize', 'maximize', 'update:window'])

// Computed para o estilo da janela
const windowStyle = computed(() => ({
  left: props.window.position.x + 'px',
  top: props.window.position.y + 'px',
  width: props.window.size.width + 'px',
  height: props.window.size.height + 'px',
  zIndex: props.window.focused ? 50 : 40,
  borderColor: props.window.focused ? '#3b82f6' : '#d1d5db'
}))

// Variáveis para arrastar e redimensionar
let isDragging = false
let isResizing = false
let resizeDirection = ''
let startX = 0
let startY = 0
let startWidth = 0
let startHeight = 0
let startLeft = 0
let startTop = 0

const closeWindow = () => emit('close', props.window.id)

const minimizeWindow = () => emit('minimize', props.window.id)
const maximizeWindow = () => emit('maximize', props.window.id)
const decreaseWindow = () => emit('decrease', props.window.id)
const focusWindow = () => emit('focus', props.window.id)

const startDrag = (e) => {
  if (e.target.tagName === 'BUTTON') return
  
  focusWindow()
  isDragging = true
  startX = e.clientX
  startY = e.clientY
  startLeft = props.window.position.x
  startTop = props.window.position.y
  
  document.addEventListener('mousemove', handleDrag)
  document.addEventListener('mouseup', stopDrag)
  e.preventDefault()
}

const handleDrag = (e) => {
  if (!isDragging) return
  
  const dx = e.clientX - startX
  const dy = e.clientY - startY
  
  emit('update:window', {
    ...props.window,
    position: {
      x: Math.max(0, startLeft + dx),
      y: Math.max(0, startTop + dy)
    }
  })
}

const stopDrag = () => {
  isDragging = false
  document.removeEventListener('mousemove', handleDrag)
  document.removeEventListener('mouseup', stopDrag)
}

const startResize = (direction) => {
  focusWindow()
  isResizing = true
  resizeDirection = direction
  startX = event.clientX
  startY = event.clientY
  startWidth = props.window.size.width
  startHeight = props.window.size.height
  startLeft = props.window.position.x
  startTop = props.window.position.y
  
  document.addEventListener('mousemove', handleResize)
  document.addEventListener('mouseup', stopResize)
  event.preventDefault()
}

const handleResize = (e) => {
  if (!isResizing) return
  
  const dx = e.clientX - startX
  const dy = e.clientY - startY
  let newWidth = startWidth
  let newHeight = startHeight
  let newX = startLeft
  let newY = startTop

  // Lógica de redimensionamento baseada na direção
  if (resizeDirection.includes('e')) {
    newWidth = Math.max(200, startWidth + dx)
  }
  if (resizeDirection.includes('w')) {
    newWidth = Math.max(200, startWidth - dx)
    newX = startLeft + dx
  }
  if (resizeDirection.includes('s')) {
    newHeight = Math.max(150, startHeight + dy)
  }
  if (resizeDirection.includes('n')) {
    newHeight = Math.max(150, startHeight - dy)
    newY = startTop + dy
  }

  emit('update:window', {
    ...props.window,
    position: {
      x: Math.max(0, newX),
      y: Math.max(0, newY)
    },
    size: {
      width: newWidth,
      height: newHeight
    }
  })
}

const stopResize = () => {
  isResizing = false
  document.removeEventListener('mousemove', handleResize)
  document.removeEventListener('mouseup', stopResize)
}

// Cleanup listeners quando o componente for destruído
onUnmounted(() => {
  document.removeEventListener('mousemove', handleDrag)
  document.removeEventListener('mouseup', stopDrag)
  document.removeEventListener('mousemove', handleResize)
  document.removeEventListener('mouseup', stopResize)
})
</script>

<style scoped>
/* Estilos para os handles de redimensionamento */
.cursor-n-resize {
  cursor: n-resize;
}

.cursor-s-resize {
  cursor: s-resize;
}

.cursor-w-resize {
  cursor: w-resize;
}

.cursor-e-resize {
  cursor: e-resize;
}

.cursor-nw-resize {
  cursor: nw-resize;
}

.cursor-ne-resize {
  cursor: ne-resize;
}

.cursor-sw-resize {
  cursor: sw-resize;
}

.cursor-se-resize {
  cursor: se-resize;
}

/* Melhorar a usabilidade dos handles */
.cursor-n-resize:hover,
.cursor-s-resize:hover,
.cursor-w-resize:hover,
.cursor-e-resize:hover,
.cursor-nw-resize:hover,
.cursor-ne-resize:hover,
.cursor-sw-resize:hover,
.cursor-se-resize:hover {
  background-color: rgba(59, 130, 246, 0.2);
}

/* Estilos para operações em andamento */
.window-dragging {
  opacity: 0.9;
  box-shadow: 0 0 30px rgba(0, 0, 0, 0.4);
}

.window-resizing {
  opacity: 0.9;
  transition: none !important;
}

.no-select {
  user-select: none;
  -webkit-user-select: none;
  -moz-user-select: none;
  -ms-user-select: none;
}
</style>