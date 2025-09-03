<template>
  <div class="hover:bg-gray-600 flex flex-col items-center w-16 cursor-pointer transition transform hover:scale-105" @click="$emit('click')">
    <div class=" w-12 h-12 flex items-center justify-center mb-1 shadow-md">
      <span class="text-white text-2xl">{{ getIcon }}</span>
    </div>
    <span class="text-white text-xs text-center font-medium text-shadow">{{ label }}</span>
  </div>
</template>

<script setup>
const props = defineProps({
  icon: String,
  label: String
})

defineEmits(['click'])

const getIcon = {
  folder: '📁',
  terminal: '💻',
  browser: '🌐',
  settings: '⚙️'
}[props.icon] || '📄'
</script>

<style scoped>
.text-shadow {
  text-shadow: 0 1px 2px rgba(0,0,0,0.8);
}
</style>