<script setup>
import { watch, onMounted, ref, reactive } from 'vue'
import axios from 'axios'

import Header from './components/Header.vue'
import CardList from './components/CardList.vue'
import Drawer from './components/Drawer.vue'

const items = ref([])

const filters = reactive({
  sortBy: 'name',
  searchQuery: '',
})

const onChangeSelect = (event) => {
  filters.sortBy = event.target.value;
}

const onChacngeSearchInput = (event) => {
  filters.searchQuery = event.target.value;
}

const fetchFavorites = async () => {
  try {
    const { data: favorites } = await axios.get(`https://ea69bf7e8b434416.mokky.dev/favorites`)

    items.value = items.value.map(item => {
      const favorite = favorites.find((favorite) => favorite.parentId === item.id)

      if (!favorite) {
        return item;
      }

      return {
        ...item,
        isFavorite: true,
        favoriteId: favorite.id,
      };
    });

    console.log(items.value)
  } catch (err) {
    console.log(err)
  }
}

const addToFavorite = async (item) => {
  item.isFavorite = true
}

const fetchItems = async () => {
  try {
    const params = {
      sortBy: filters.sortBy
    }

    if (filters.searchQuery) {
      params.name = `*${filters.searchQuery}*`
    }

    const { data } = await axios.get(`https://ea69bf7e8b434416.mokky.dev/items`, {
      params
    })

    items.value = data.map((obj) => ({
      ...obj,
      isFavorite: false,
      isAdded: false
    }))
  } catch (err) {
    console.log(err)
  }
}

onMounted(async() => {
  await fetchItems();
  await fetchFavorites();
})
watch(filters, fetchItems);

</script>

<template>
  <!-- <Drawer /> -->
  <div class="bg-white w-4/5 m-auto rounded-xl shadow-xl mt-14">
    <Header />

    <div class="p-10">
      <div class="flex justify-between items-center">
        <h2 class="text-3xl font-bold mb-10">Все кроссовки</h2>

        <div class="flex items-center gap-4">
          <select @change="onChangeSelect" class="py-2 px-3 border rounded-md outline-none">
            <option value="name">По названию</option>
            <option value="price">По цене (дешевле)</option>
            <option value="-price">По цене (дороже)</option>
          </select>
  
          <div class="relative">
            <img
              class="absolute top-1/2 -translate-y-1/2 left-3"
              src="/img/search.svg" 
              alt="">
            <input
              @change="onChacngeSearchInput"
              class="border rounded-md py-2 pl-9 pr-4 outline-none focus:border-gray-400 transition-all"
              type="search" 
              name="" 
              id="" 
              placeholder="Поиск..."
            >
          </div>
        </div>
      </div>

      <CardList :items="items" />
    </div>
  </div>
</template>

<style>
  body {
    background-color: rgb(247, 254, 231);
  }
</style>
