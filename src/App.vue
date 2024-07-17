<script setup>
import { watch, onMounted, ref, reactive, provide, computed } from 'vue'
import axios from 'axios'

import Header from './components/Header.vue'
import CardList from './components/CardList.vue'
import Drawer from './components/Drawer.vue'
import CartItemList from './components/CartItemList.vue'

const items = ref([])
const cart = ref([])
const isCreatingOrder = ref(false)

const cartIsEmpty = computed(() => 
  cart.value.length === 0
)

const cartButtonDisabled = computed(() => 
  isCreatingOrder.value || cartIsEmpty.value
)

const drawerOpen = ref(false)
const closeDrawer = () => {
  drawerOpen.value = false
}
const openDrawer = () => {
  drawerOpen.value = true
}

const totalPrice = computed(
  () => cart.value.reduce((acc, item) => acc + item.price, 0)
)

const vatPrice = computed(
  () => Math.round((totalPrice.value * 5) / 100)  
)

const filters = reactive({
  sortBy: 'name',
  searchQuery: '',
})

const addToCart = (item) => {
  cart.value.push(item)
  item.isAdded = true
}

const removeFromCart = (item) => {
  cart.value.splice(cart.value.indexOf(item), 1)
  item.isAdded = false
}

const onClickAddAddPlus = (item) => {
  if (!item.isAdded) {
    addToCart(item)
  } else {
    removeFromCart(item)
  }

  console.log(cart)
}

const createOrder = async () => {
  try {
    isCreatingOrder.value = true
    const { data } = await axios.post(`https://ea69bf7e8b434416.mokky.dev/orders`, {
      items: cart.value,
      totalPrice: totalPrice.value,
    })

    cart.value = []

    return data;
  } catch(err) {
    console.log(err)
  } finally {
    isCreatingOrder.value = false
  }
}

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
  } catch (err) {
    console.log(err)
  }
}

const addToFavorite = async (item) => {
  try {
    if (!item.isFavorite) {
      const obj = {
        parentId: item.id,
      };

      item.isFavorite = true;

      const { data } = await axios.post(`https://ea69bf7e8b434416.mokky.dev/favorites`, obj)

      item.favoriteId = data.id;
    } else {
      item.isFavorite = false;
      await axios.delete(`https://ea69bf7e8b434416.mokky.dev/favorites/${item.favoriteId}`)
      item.favoriteId = null;
    }

  } catch(err){
    console.log(err)
  }
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
      favoriteId: null,
      isAdded: false
    }))
  } catch (err) {
    console.log(err)
  }
}

onMounted(async() => {
  const localCart = localStorage.getItem('cart');
  cart.value = localCart ? JSON.parse(localCart) : '[]';

  await fetchItems();
  await fetchFavorites();

  items.value = items.value.map((item) => ({
    ...item,
    isAdded: cart.value.some((cartItem) => cartItem.id === item.id)
  }))
})

watch(filters, fetchItems);

watch(cart, () => {
  items.value = items.value.map((item) => ({
    ...item,
    isAdded: false
  }))
})

watch(cart, () => {
  localStorage.setItem('cart', JSON.stringify(cart.value))
}, {
  deep: true
})

provide('cart', {
  cart,
  closeDrawer,
  openDrawer,
  addToCart,
  removeFromCart,
})

</script>

<template>
  <Drawer v-if="drawerOpen" :total-price="totalPrice" :vat-price="vatPrice" @create-order="createOrder" :button-disabled="cartButtonDisabled" />
  <div class="bg-white w-4/5 m-auto rounded-xl shadow-xl mt-14">
    <Header :total-price="totalPrice" @open-drawer="openDrawer" />

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

      <CardList :items="items" @add-to-favorite="addToFavorite" @add-to-cart="onClickAddAddPlus" />
    </div>
  </div>
</template>

<style>
  body {
    background-color: rgb(247, 254, 231);
  }
</style>
