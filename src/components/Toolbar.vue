<template>
    <div v-bind="$attrs" class="fixed right-4 bottom-10 w-64 h-80 bg-gray-800 shadow-lg z-50 flex flex-col justify-center items-center">
        <div class="flex space-x-2 mb-2">
            <button class="px-3 py-1 bg-gray-700 rounded hover:bg-gray-600 text-white">Arquivo</button>
            <button class="px-3 py-1 bg-gray-700 rounded hover:bg-gray-600 text-white">Editar</button>
            <button class="px-3 py-1 bg-gray-700 rounded hover:bg-gray-600 text-white">Ajuda</button>
        </div>
        <div class="flex space-x-2">
            <button class="px-3 py-1 bg-green-700 rounded hover:bg-green-600 text-white">Novo</button>
            <button class="px-3 py-1 bg-blue-700 rounded hover:bg-blue-600 text-white">Salvar</button>
            <button class="px-3 py-1 bg-red-700 rounded hover:bg-red-600 text-white">Fechar</button>
        </div>
    </div>
</template>

<script setup>
defineOptions({ inheritAttrs: false })
</script>