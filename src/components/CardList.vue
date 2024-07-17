<script setup>
import Card from './Card.vue'

defineProps({
    items: Array
})

const emit = defineEmits(['addToFavorite', 'addToCart']);
</script>

<template>
    <div class="grid grid-cols-4 gap-10 mt-10" v-auto-animate>
        <Card 
            v-for="item in items"
            :key="item.id"
            :id="item.id"
            :image-url="item.imageUrl" 
            :title="item.name" 
            :price="item.price"
            :onClickFavorite="() => emit('addToFavorite', item)"
            :onClickAdd="() => emit('addToCart', item)"
            :isFavorite="item.isFavorite"
            :isAdded="item.isAdded"
        />

        
        <!-- <Card image-url="./img/sneakers/2.jpg" title="Мужские Кроссовки Nike Air Max 270" :price="12990" />
        <Card image-url="./img/sneakers/3.jpg" title="Мужские Кроссовки Nike Blazer Mid Suede" :price="12990" />
        <Card image-url="./img/sneakers/4.jpg" title="Кроссовки Puma X Aka Boku Future Rider" :price="12990" />
        <Card image-url="./img/sneakers/5.jpg" title="Мужские Кроссовки Under Armour Curry 8" :price="12990" /> -->
    </div>
</template>