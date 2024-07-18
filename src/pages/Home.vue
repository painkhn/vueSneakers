<script setup>
    import { inject, reactive, watch, ref, onMounted, provide } from 'vue'
    import axios from 'axios'
    import CardList from '../components/CardList.vue'

    const { cart, addToCart, removeFromCart } = inject('cart')

    const items = ref([])

    const onClickAddPlus = (item) => {
        if (!item.isAdded) {
            addToCart(item)
        } else {
            removeFromCart(item)
        }

        console.log(cart)
    }

    const onChangeSelect = (event) => {
        filters.sortBy = event.target.value;
    }

    const onChacngeSearchInput = (event) => {
        filters.searchQuery = event.target.value;
    }

    const addToFavorite = async (item) => {
        try {
            if (!item.isFavorite) {
                const obj = {
                    item_id: item.id,
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

    const filters = reactive({
        sortBy: 'name',
        searchQuery: '',
    })

    const fetchFavorites = async () => {
        try {
            const { data: favorites } = await axios.get(`https://ea69bf7e8b434416.mokky.dev/favorites`)

            items.value = items.value.map((item) => {
            const favorite = favorites.find((favorite) => favorite.item_id === item.id)

            if (!favorite) {
                return item
            }

            return {
                ...item,
                isFavorite: true,
                favoriteId: favorite.id
            }
            })
        } catch (err) {
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

    onMounted(async () => {
        const localCart = localStorage.getItem('cart')
        cart.value = localCart ? JSON.parse(localCart) : '[]'

        await fetchItems()
        await fetchFavorites()

        items.value = items.value.map((item) => ({
            ...item,
            isAdded: cart.value.some((cartItem) => cartItem.id === item.id)
        }))
    })

    watch(cart, () => {
        items.value = items.value.map((item) => ({
            ...item,
            isAdded: false
        }))
    })

    watch(filters, fetchItems);

</script>

<template>
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
                    alt=""
                >
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

    <CardList :items="items" @add-to-favorite="addToFavorite" @add-to-cart="onClickAddPlus" />
</template>