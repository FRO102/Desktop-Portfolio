<template>
  <div v-bind="$attrs" class="fixed right-4 w-64 h-80 bg-gray-800 shadow-lg z-50 flex flex-col justify-center items-center">
    <div class="w-full h-full overflow-auto p-4 space-y-6">
      <Icon icon="documents" label="Documents" @click="openWindow('documents')" />
      <Icon icon="terminal" label="Terminal" @click="openWindow('terminal')" />
      <Icon icon="browser" label="Browser" @click="openWindow('browser')" />
      <Icon icon="settings" label="Settings" @click="openWindow('settings')" />
    </div>
  </div>
</template>

<script setup>
import Icon from './Icon.vue'

const props = defineProps({
  windows: {
    type: Array,
    default: () => []
  }
})

const emit = defineEmits(['open-window'])
const openWindow = (type) => emit('open-window', type)
</script>