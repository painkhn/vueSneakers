<script setup>
  defineProps({
    totalPrice: Number
  })

  const emit = defineEmits(['openDrawer']);


</script>

<template>
  <header class="flex items-center justify-between border-b border-slate-100 px-12 py-11">
    
    <router-link to="/">
      <div class="flex items-center gap-4">
        <img class="w-10 h-10" src="/img/logo.png" alt="" />
        <div>
          <h2 class="text-xl font-bold uppercase">Vue Sneakers</h2>
          <p class="text-slate-400">Магазин лучших кроссовок</p>
        </div>
      </div>
    </router-link>
    

    <nav>
      <ul class="flex items-center gap-8">

        <li @click="() => emit('openDrawer')" class="flex items-center cursor-pointer gap-2 text-gray-500 hover:text-black font-bold">
          <img src="/img/cart.svg" alt="" />
          <p>{{ totalPrice }} руб.</p>
        </li>

        <router-link to="/favorites">
          <li class="flex items-center cursor-pointer gap-2 text-gray-500 hover:text-black">
            <img src="/img/heart.svg" alt="" />
            <p>Закладки</p>
          </li>
        </router-link>

        <li class="flex items-center cursor-pointer gap-2 text-gray-500 hover:text-black">
          <img src="/img/user.svg" alt="" />
          <p>Профиль</p>
        </li>

      </ul>
    </nav>
  </header>
</template>
