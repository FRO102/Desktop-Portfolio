<template>
  <div class="absolute bottom-0 left-0 right-0 bg-black border-t border-gray-700 px-4 py-1 flex items-center justify-between">
    <!-- Botão Iniciar -->
    <button class="bg-gradient-to-r from-green-600 to-green-500 px-4 py-1 text-white text-sm font-medium flex items-center space-x-2">
      <span>🐧</span>
      <span>Iniciar</span>
    </button>

    <!-- Janelas abertas -->
    <div class="flex space-x-2 flex-1 mx-4">
      <button 
        v-for="window in windows" 
        :key="window.id"
        class="px-3 py-1 text-sm font-medium flex items-center space-x-2 transition"
        :class="window.focused ? ' bg-stone-900 border-white border border-solid text-white' : 'border-white border border-solid  bg-black text-white hover:bg-gray-600'"
        @click="handleTaskbarClick(window)"
      >
        <span>📋</span>
        <span>{{ window.title.split(' - ')[0] }}</span>
      </button>
    </div>

    <!-- Área de notificação -->
    <div class="flex items-center space-x-3 text-white">
      <span class="text-xs px-2 py-1">{{ currentTime }} - {{ currentDate }}</span>

    </div>
  </div>
</template>

<script setup>

import { ref,defineProps, onMounted, onUnmounted } from 'vue'

const props = defineProps({
  windows: {
    type: Array,
    default: () => []
  }
})

const emit = defineEmits(['focus','minimize','restore'])
const focusWindow = (id) => emit('focus', id)
const minimizeWindow = (id) => emit('minimize', id)
const restoreWindow = (id) => emit('restore', id)

// Alterna entre minimizar/restaurar ao clicar no botão da Taskbar
function handleTaskbarClick(window) {

  if (window.minimized) {
    restoreWindow(window.id)
    focusWindow(window.id)
  } else {
    minimizeWindow(window.id)
  }
}

const currentTime = ref('')
const currentDate = ref('')
const updateDateTime = () => {
  const now = new Date()
  currentTime.value = now.toLocaleTimeString('pt-PT', { hour: '2-digit', minute: '2-digit', second: '2-digit' })
  currentDate.value = now.toLocaleDateString('pt-PT', { day: '2-digit', month: '2-digit', year: 'numeric' })
}
let intervalId
onMounted(() => {
  updateDateTime()
  intervalId = setInterval(updateDateTime, 1000)
})
onUnmounted(() => {
  if (intervalId) {
    clearInterval(intervalId)
  }
})
</script>