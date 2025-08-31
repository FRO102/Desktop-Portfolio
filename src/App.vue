<template>
  <div id="linux-desktop" class="h-screen bg-gray-900 overflow-hidden select-none">
    <Desktop />
  </div>
</template>

<script setup>
import Desktop from './components/Desktop.vue'
</script>

<style>
#linux-desktop {
  font-family: 'Ubuntu', 'DejaVu Sans', sans-serif;
}
</style>