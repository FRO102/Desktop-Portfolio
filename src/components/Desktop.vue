<template>
  <div class="h-full bg-gradient-to-br from-gray-800 to-gray-900 relative">
    <!-- Wallpaper -->
    <div class="absolute inset-0 bg-cover bg-center opacity-20" style="background-image: url('data:image/svg+xml;base64,PHN2ZyB3aWR0aD0iNjQiIGhlaWdodD0iNjQiIHZpZXdCb3g9IjAgMCA2NCA2NCIgeG1sbnM9Imh0dHA6Ly93d3cudzMub3JnLzIwMDAvc3ZnIj48cGF0aCBkPSJNMTYgMTZINDhWNDhIMTZWMTZaIiBmaWxsPSIjMzMzIiBmaWxsLXJ1bGU9ImV2ZW5vZGQiLz48L3N2Zz4=')"></div>
    
    <!-- Ícones do Desktop -->
    <div class="absolute top-4 left-4 space-y-6">
      <Icon icon="folder" label="Documentos" @click="openWindow('documents')" />
      <Icon icon="terminal" label="Terminal" @click="openWindow('terminal')" />
      <Icon icon="browser" label="Navegador" @click="openWindow('browser')" />
      <Icon icon="settings" label="Configurações" @click="openWindow('settings')" />
    </div>

    <!-- Janelas -->
    <Window 
      v-for="window in windows" 
      :key="window.id"
      :window="window"
      @close="closeWindow(window.id)"
      @focus="focusWindow(window.id)"
    />

    <!-- Taskbar -->
    <Taskbar 
      :windows="windows"
      @open-window="openWindow"
      @focus-window="focusWindow"
    />
  </div>
</template>

<script setup>
import { ref } from 'vue'
import Icon from './Icon.vue'
import Window from './Window.vue'
import Taskbar from './Taskbar.vue'

// REMOVA defineProps se estiver definindo 'msg' aqui
// defineProps(['msg']) // ← REMOVA ESTA LINHA SE EXISTIR

const windows = ref([])
let windowId = 0

const openWindow = (type) => {
  const id = windowId++
  windows.value.push({
    id,
    type,
    title: getWindowTitle(type),
    content: getWindowContent(type),
    position: { x: 100 + windows.value.length * 20, y: 100 + windows.value.length * 20 },
    size: { width: 500, height: 300 },
    focused: true,
    minimized: false
  })
}

const closeWindow = (id) => {
  windows.value = windows.value.filter(w => w.id !== id)
}

const focusWindow = (id) => {
  windows.value = windows.value.map(w => ({
    ...w,
    focused: w.id === id,
    minimized: w.id === id ? false : w.minimized
  }))
}

const getWindowTitle = (type) => {
  const titles = {
    terminal: 'Terminal - fro@linux-desktop',
    browser: 'Navegador Web',
    documents: 'Documentos',
    settings: 'Configurações do Sistema'
  }
  return titles[type] || 'Janela'
}

const getWindowContent = (type) => {
  const contents = {
    terminal: 'fro@linux-desktop:~$ echo "Bem-vindo ao meu desktop virtual!"\n> Sistema funcionando perfeitamente!\n> Use os ícones ou o menu iniciar para explorar.',
    browser: '<h1>Bem-vindo ao Navegador</h1><p>Este é um navegador web simulado.</p>',
    documents: '📁 Pasta de Documentos\n├── 📄 relatorio.pdf\n├── 📄 projeto-vue.txt\n└── 📁 imagens',
    settings: '⚙️ Configurações do Sistema\n\n• Tema: Escuro\n• Resolução: 1920x1080\n• Som: Ativado'
  }
  return contents[type] || 'Conteúdo da janela'
}
</script>