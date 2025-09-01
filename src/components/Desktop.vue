<template>
  <div class="h-full bg-gradient-to-br bg-stone-900 relative">
    <!-- Wallpaper -->
    <div class="absolute inset-0 bg-cover bg-center opacity-0" style="background-image: url('data:image/svg+xml;base64,PHN2ZyB3aWR0aD0iNjQiIGhlaWdodD0iNjQiIHZpZXdCb3g9IjAgMCA2NCA2NCIgeG1sbnM9Imh0dHA6Ly93d3cudzMub3JnLzIwMDAvc3ZnIj48cGF0aCBkPSJNMTYgMTZINDhWNDhIMTZWMTZaIiBmaWxsPSIjMzMzIiBmaWxsLXJ1bGU9ImV2ZW5vZGQiLz48L3N2Zz4=')"></div>
    
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
      @close="closeWindow"
      @focus="focusWindow"
      @minimize="minimizeWindow"
      @update:window="(updates) => updateWindow(window.id, updates)"
    />

    <!-- Taskbar -->
<Taskbar 
  :windows="windows"
  @minimize="minimizeWindow"
  @restore="restoreWindow"
  @focus="focusWindow"
/>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import Icon from './Icon.vue'
import Window from './Window.vue'
import Taskbar from './Taskbar.vue'

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

function minimizeWindow(id) {
  windows.value = windows.value.map(w =>
    w.id === id ? { ...w, minimized: true } : w
  )
}

function restoreWindow(id) {
  windows.value = windows.value.map(w =>
    w.id === id ? { ...w, minimized: false } : w
  )
}

function closeWindow(id) {
  windows.value = windows.value.filter(w => w.id !== id)
}

function focusWindow(id) {
  windows.value = windows.value.map(w =>
    w.id === id ? { ...w, focused: true, minimized: false } : w
  )
}

const updateWindow = (windowId, updates) => {
  windows.value = windows.value.map(w => 
    w.id === windowId ? { ...w, ...updates } : w
  )
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
    terminal: 'fro@linux-desktop:~$ echo "Bem-vindo ao meu desktop virtual!"\n> Sistema funcionando perfeitamente!\n> Use os ícones ou o menu iniciar para explorar.\n\n> Comandos disponíveis:\n> - ls: listar arquivos\n> - cd: mudar diretório\n> - pwd: diretório atual\n> - exit: fechar terminal',
    browser: '<div class="p-4"><h1 class="text-2xl font-bold text-blue-800 mb-4">🌐 Navegador Web</h1><p class="text-gray-700">Bem-vindo ao navegador virtual!</p><ul class="mt-4 list-disc list-inside"><li>📧 Email Web</li><li>📰 Notícias</li><li>🎵 Música Online</li></ul></div>',
    documents: '📁 Pasta de Documentos\n├── 📄 relatorio.pdf (2.4 MB)\n├── 📄 projeto-vue.txt (15 KB)\n├── 📄 curriculum.pdf (1.1 MB)\n├── 📁 Trabalho\n│   ├── 📄 apresentacao.pptx\n│   └── 📄 relatorio_final.doc\n└── 📁 imagens\n    ├── 🖼️ screenshot1.png\n    └── 🖼️ wallpaper.jpg',
    settings: '⚙️ Configurações do Sistema\n\n• Tema: Escuro\n• Resolução: 1920x1080\n• Som: Ativado\n• Brilho: 80%\n• Rede: Conectado - WiFi\n• Bateria: 95% (Carregando)\n\n📊 Uso do Sistema:\n• CPU: 12%\n• Memória: 4.2GB/16GB\n• Disco: 128GB/512GB'
  }
  return contents[type] || 'Conteúdo da janela'
}
</script>

<style scoped>
/* Estilos específicos do desktop */
</style>