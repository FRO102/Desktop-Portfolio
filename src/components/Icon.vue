<template>
  <div class="hover:bg-gray-600 flex flex-col items-center w-16 cursor-pointer transition transform hover:scale-105" @click="$emit('click')">
    <div class="w-12 h-12 flex items-center justify-center mb-1 shadow-md">
      <span class="text-white text-2xl">
        <img v-if="iconImg" :src="iconImg" :alt="label + ' Icon'" class="w-8 h-8" />
        <template v-else>{{ getIcon }}</template>
      </span>
    </div>
    <span class="text-white text-xs text-center font-medium text-shadow">{{ label }}</span>
  </div>
</template>

<script setup>
import terminalImg from './icons/terminal.png'
import browserImg from './icons/browser.png'
import documentsImg from './icons/documents.png'
import settingsImg from './icons/settings.png'


const props = defineProps({
  icon: String,
  label: String
})

defineEmits(['click'])

const iconImages = {
  terminal: terminalImg,
  browser: browserImg,
  documents: documentsImg,
  settings: settingsImg
  // Add more image imports and mappings here as needed
}

const getIcon = {
  documents: '📁',
  terminal: '💻',
  browser: '🌐',
  settings: '⚙️'
}[props.icon] || '📄'

import { computed } from 'vue'

const iconImg = computed(() => iconImages[props.icon] || null)
</script>

<style scoped>
.text-shadow {
  text-shadow: 0 1px 2px rgba(0,0,0,0.8);
}
</style>