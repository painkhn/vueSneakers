<script setup>
    const emit = defineEmits('onClickRemove')

    defineProps({
        id: Number,
        title: String,
        imageUrl: String,
        price: Number
    })
</script>

<template>
    <div class="flex items-center gap-5 w-full border border-slate-200 p-4 rounded-xl">
        <img class="w-16 h-16" :src="imageUrl" alt="title" />

        <dir class="flex flex-col pl-0 flex-1">
            <p>{{ title }}</p>

            <div class="flex justify-between mt-2">
                <p class="font-bold flex-1">{{ price }} руб.</p>
                <img @click="emit('onClickRemove')" class="opacity-40 hover:opacity-100 transition-all cursor-pointer" src="/img/btn-remove.svg" alt="" />
            </div>
        </dir>
    </div>
</template>