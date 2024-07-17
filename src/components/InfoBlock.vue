<script setup>
defineProps({
    title: String,
    description: String,
    imageUrl: String,
})
</script>

<template>
    <div class="flex flex-col items-center text-center w-72 m-auto">
        <img class="w-40" :src="imageUrl" alt="Info image" />
        <h2 class="mt-4 text-2xl form-medium">{{ title }}</h2>
        <p class="text-gray-400 mt-2">{{ description }}</p>
    </div>
</template>