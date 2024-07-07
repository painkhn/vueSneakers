<script setup>
    import DrawerHead from './DrawerHead.vue'
    import CartItemList from './CartItemList.vue'
</script>

<template>
    <div class="fixed top-0 left-0 h-full w-full bg-black z-10 opacity-70"></div>
    <div class="bg-white w-96 h-full fixed right-0 top-0 z-20 p-8 flex flex-col">
        <DrawerHead />
        <CartItemList />

        <div class="flex-1"></div>

        <div class="flex flex-col gap-4 mt-8">

            <div class="flex gap-2">
                <span>Итого: </span>
                <div class="flex-1 border-b border-dashed"></div>
                <span class="font-bold">12900 ₽</span>
            </div>

            <div class="flex gap-2">
                <span>Налог 5%: </span>
                <div class="flex-1 border-b border-dashed"></div>
                <span class="font-bold">900 ₽</span>
            </div>

            <button class="bg-lime-500 mt-4 w-full rounded-xl py-3 text-white font-bold transition-all active:bg-lime-700 hover:bg-lime-600 disabled:bg-slate-400">Оформить заказ</button>

        </div>
    </div>
</template>