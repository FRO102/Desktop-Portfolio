<template>
  <div class="absolute bottom-0 left-0 right-0 bg-gray-800 border-t border-gray-700 px-4 py-2 flex items-center justify-between">
    <!-- Botão Iniciar -->
    <button class="bg-gradient-to-r from-green-600 to-green-500 px-4 py-2 rounded text-white text-sm font-medium flex items-center space-x-2">
      <span>🐧</span>
      <span>Iniciar</span>
    </button>

    <!-- Janelas abertas -->
    <div class="flex space-x-2 flex-1 mx-4">
      <button 
        v-for="window in windows" 
        :key="window.id"
        class="px-3 py-1 rounded text-sm font-medium flex items-center space-x-2 transition"
        :class="window.focused ? 'bg-blue-600 text-white' : 'bg-gray-700 text-gray-300 hover:bg-gray-600'"
        @click="focusWindow(window.id)"
      >
        <span>📋</span>
        <span>{{ window.title.split(' - ')[0] }}</span>
      </button>
    </div>

    <!-- Área de notificação -->
    <div class="flex items-center space-x-3 text-white">
      <span>🕒 19:45</span>
      <span>🔊</span>
      <span>🌐</span>
    </div>
  </div>
</template>

<script setup>
defineProps({
  windows: Array
})

defineEmits(['open-window', 'focus-window'])
</script>