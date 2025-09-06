<template>
  <div class="h-full bg-gradient-to-br bg-stone-900 relative">
    <!-- Wallpaper -->
    <div class="absolute inset-0 bg-cover bg-center opacity-0" style="background-image: url('data:image/svg+xml;base64,PHN2ZyB3aWR0aD0iNjQiIGhlaWdodD0iNjQiIHZpZXdCb3g9IjAgMCA2NCA2NCIgeG1sbnM9Imh0dHA6Ly93d3cudzMub3JnLzIwMDAvc3ZnIj48cGF0aCBkPSJNMTYgMTZINDhWNDhIMTZWMTZaIiBmaWxsPSIjMzMzIiBmaWxsLXJ1bGU9ImV2ZW5vZGQiLz48L3N2Zz4=')"></div>
    
    <!-- Ícones do Desktop 
    <div class="absolute top-4 left-4 space-y-6">
      <Icon icon="documents" label="Documentos" @click="openWindow('documents')" />
      <Icon icon="terminal" label="Terminal" @click="openWindow('terminal')" />
      <Icon icon="browser" label="Navegador" @click="openWindow('browser')" />
      <Icon icon="settings" label="Configurações" @click="openWindow('settings')" />
    </div>
    -->
    <Icon icon="terminal" label="My Terminal" @click="openWindow('myterminal')" />
    <!-- Janelas -->
    <Window 
      v-for="window in windows" 
      :key="window.id"
      :window="window"
      @close="closeWindow"
      @focus="focusWindow"
      @minimize="minimizeWindow"
      @maximize="maximizeWindow"
      @decrease="decreaseWindow"
      @update:window="(updates) => updateWindow(window.id, updates)"
    />

  <!-- Toolbar flutuante -->
  <Toolbar v-if="showToolbar" class="absolute left-4 z-50" style="bottom: 45px;" @open-window="openWindow" />

    <!-- Taskbar -->
<Taskbar 
  :windows="windows"
  @minimize="minimizeWindow"
  @restore="restoreWindow"
  @focus="focusWindowBar"
  @open-toolbar="toggleToolbar"
/>
  </div>
</template>

<script setup>
import { ref, markRaw } from 'vue'
import Toolbar from './Toolbar.vue'
import Icon from './Icon.vue'
import Window from './Window.vue'
import Taskbar from './Taskbar.vue'
import TerminalContent from './TerminalContent.vue'
import BrowserContent from './BrowserContent.vue'
import DocumentsContent from './DocumentsContent.vue'
import SettingsContent from './SettingsContent.vue'
import MyTerminal from './MyTerminal.vue'

const showToolbar = ref(false)

function toggleToolbar() {
  showToolbar.value = !showToolbar.value
}

const windows = ref([])
let windowId = 0

const openWindow = (type) => {
  const id = windowId++
  windows.value.push({
    id,
    type,
    title: getWindowTitle(type),
    content: markRaw(getWindowContent(type)),
    position: { x: 100 + windows.value.length * 20, y: 100 + windows.value.length * 20 },
    size: { width: 500, height: 300 },
    focused: true,
    minimized: false,
    maximized: false
  })
}


function minimizeWindow(id) {
  windows.value = windows.value.map(w =>
    w.id === id ? { ...w, minimized: true } : w
  )
}

function maximizeWindow(id) {
  windows.value = windows.value.map(w =>
    w.id === id ? { ...w, size: { width: window.innerWidth - 40, height: window.innerHeight - 100 }, position: { x: 20, y: 20 } } : w
  )
  windows.value = windows.value.map(w =>
    w.id === id ? { ...w, maximized: true } : w
  )
}

function decreaseWindow(id) {
  windows.value = windows.value.map(w =>
    w.id === id ? { ...w, size: { width: Math.max(200, w.size.width - 500), height: Math.max(150, w.size.height - 250) } } : w
  )
  windows.value = windows.value.map(w =>
    w.id === id ? { ...w, maximized: false } : w
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

function focusWindowBar(id) {
  windows.value = windows.value.map(w =>
    w.id === id ? { ...w, focused: true} : w
  )
}

function focusWindow(id) {
  const idx = windows.value.findIndex(w => w.id === id)
  if (idx !== -1) {
    const win = { ...windows.value[idx], focused: true, minimized: false }
    windows.value.splice(idx, 1)
    windows.value.push(win)
  }
}

const updateWindow = (windowId, updates) => {
  windows.value = windows.value.map(w => 
    w.id === windowId ? { ...w, ...updates } : w
  )
}

const getWindowTitle = (type) => {
  const titles = {
    terminal: 'Terminal',
    browser: 'Browser',
    documents: 'Documents',
    settings: 'Settings',
    myterminal: 'My Terminal'
  }
  return titles[type] || ' '
}

const getWindowContent = (type) => {
  const components = {
    terminal: TerminalContent,
    browser: BrowserContent,
    documents: DocumentsContent,
    settings: SettingsContent,
    myterminal: MyTerminal,
  }
  return components[type] || null
}

</script>

<style scoped>
/* Estilos específicos do desktop */
</style>