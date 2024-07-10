<script setup>
import { inject } from 'vue'

const props = defineProps({
    id: Number,
    imageUrl: String,
    title: String,
    price: Number,
    isFavorite: Boolean,
    isAdded: Boolean,
    onClickFavorite: Function,
})

const onClickAdd = () => {
    alert('added')
}

const addToFavorite = inject('addToFavorite')

const onClickFavorite = () => {
    const obj = {
        parentId: props.id,
    }

    addToFavorite()
}
</script>

<template>
    <div class="justify-self-center bg-white relative border border-slate-100 rounded-3xl p-8 cursor-pointer hover:-translate-y-2 hover:shadow-xl transition-all">
        <img @click="onClickFavorite" :src="!isFavorite ? '/img/unliked.svg' : '/img/liked.svg' " alt="" class="absolute top-8 left-8" />
        <img :src="imageUrl" alt="" />
        <p>{{ title }}</p>

        <div class="flex items-center justify-between mt-5">
            <div class="flex flex-col">
                <span class="text-slate-400">Цена:</span>
                <span class="font-bold">{{ price }}₽</span>
            </div>

            <img @click="onClickAdd" :src="!isAdded ? '/img/plus.svg' : '/img/btn-checked.svg' " alt="add" />
        </div>
    </div>
</template>
