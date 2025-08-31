<template>
  <div 
    v-if="!window.minimized"
    class="absolute bg-gray-100 border border-gray-300 rounded-lg shadow-2xl overflow-hidden"
    :style="{
      left: window.position.x + 'px',
      top: window.position.y + 'px',
      width: window.size.width + 'px',
      height: window.size.height + 'px',
      zIndex: window.focused ? 50 : 40,
      borderColor: window.focused ? '#3b82f6' : '#d1d5db'
    }"
    @mousedown="focusWindow"
  >
    <!-- Barra de título -->
    <div 
      class="flex items-center justify-between px-4 py-2 bg-gradient-to-r from-blue-600 to-blue-500 text-white cursor-move"
      @mousedown="startDrag"
    >
      <div class="flex items-center space-x-2">
        <span class="text-sm">📋</span>
        <h3 class="text-sm font-medium">{{ window.title }}</h3>
      </div>
      <div class="flex space-x-2">
        <button class="w-4 h-4 bg-yellow-400 rounded-full hover:bg-yellow-300"></button>
        <button class="w-4 h-4 bg-red-500 rounded-full hover:bg-red-400" @click="closeWindow"></button>
      </div>
    </div>

    <!-- Conteúdo -->
    <div class="p-4 h-[calc(100%-40px)] bg-white overflow-auto">
      <pre v-if="window.type === 'terminal'" class="text-green-600 bg-black p-4 rounded font-mono text-sm">{{ window.content }}</pre>
      <div v-else class="text-gray-800 whitespace-pre-line">{{ window.content }}</div>
    </div>
  </div>
</template>

<script setup>
import { defineProps, defineEmits } from 'vue'

const props = defineProps({
  window: Object
})

const emit = defineEmits(['close', 'focus'])

const closeWindow = () => emit('close', props.window.id)
const focusWindow = () => emit('focus', props.window.id)

const startDrag = (e) => {
  focusWindow()
  // Implementar drag aqui (pode ser adicionado posteriormente)
}
</script>