<script setup>
    import { onMounted, ref, inject } from 'vue';
    import axios from 'axios';
    import CardList from '../components/CardList.vue';

    const favorites = ref([])

    onMounted(async () => {
        try {
            const { data } = await axios.get(`https://ea69bf7e8b434416.mokky.dev/favorites?_relations=items`)

            favorites.value = data.map(obj => obj.item)
        } catch(err) {
            console.log(err)
        }
    });
</script>

<template>
    <h2 class="text-3xl font-bold mb-10">Мои закладки</h2>
    <div>
        <CardList :items="favorites" is-favorites />
    </div>
</template>