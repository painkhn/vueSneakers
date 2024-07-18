<script setup>
    import { ref, inject, computed } from 'vue'
    import axios from 'axios'

    import DrawerHead from './DrawerHead.vue'
    import CartItemList from './CartItemList.vue'
    import InfoBlock from './InfoBlock.vue'

    const emit = defineEmits(['createOrder'])

    const props = defineProps({
        totalPrice: Number,
        vatPrice: Number
    })

    const { cart, closeDrawer } = inject('cart')
    const cartButtonDisabled = computed(() => isCreatingOrder.value || cartIsEmpty.value)
    const cartIsEmpty = computed(() => cart.value.length === 0)
    const isCreatingOrder = ref(false)
    const orderId = ref(null)

    const createOrder = async () => {
        try {
            isCreatingOrder.value = true
            
            const { data } = await axios.post(`https://ea69bf7e8b434416.mokky.dev/orders`, {
            items: cart.value,
            totalPrice: props.totalPrice.value
            })

            cart.value = []

            orderId.value = data.id;
        } catch (err) {
            console.log(err)
        } finally {
            isCreatingOrder.value = false
        }
    }

</script>

<template>
    <div class="fixed top-0 left-0 h-full w-full bg-black z-10 opacity-70"></div>
    <div class="bg-white w-96 h-full fixed right-0 top-0 z-20 p-8 flex flex-col">
        <DrawerHead />

        <div v-if="!totalPrice || orderId" class="flex flex-col h-full">
            <InfoBlock v-if="!totalPrice && !orderId" title="Корзина пустая" description="Добавьте хотя бы одну пару кроссовок, чтобы сделать заказ" image-url="/img/empty-cart.jpg" />
            <InfoBlock v-if="orderId" title="Заказ оформлен" :description="`Ваш заказ #${orderId} скоро будет доставлен курьерской службой`" image-url="/img/complete-order.jpg" />
        </div>

        <CartItemList />

        <div class="flex-1"></div>

        <div v-if="totalPrice" class="flex flex-col gap-4 mt-8">

            <div class="flex gap-2">
                <span>Налог 5%: </span>
                <div class="flex-1 border-b border-dashed"></div>
                <span class="font-bold">{{ vatPrice }} руб.</span>
            </div>

            <div class="flex gap-2">
                <span>Итого: </span>
                <div class="flex-1 border-b border-dashed"></div>
                <span class="font-bold">{{ totalPrice + vatPrice }} руб.</span>
            </div>

            

            <button class="bg-lime-500 mt-4 w-full rounded-xl py-3 text-white font-bold transition-all active:bg-lime-700 hover:bg-lime-600 disabled:bg-slate-400" :disabled="cartButtonDisabled" @click="createOrder">Оформить заказ</button>

        </div>
    </div>
</template>