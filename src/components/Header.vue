<template>
  <header class="flex justify-between border-b border-slate-200 px-10 py-8">
    <router-link to="/">
      <div class="flex items-center gap-4">
        <img class="w-10" :src="getImageUrl('/logo.png')" alt="Logo" />
        <div>
          <h2 class="text-xl font-bold uppercase">Vue Sneakers</h2>
          <p class="text-slate-400">Магазин лучших кроссовок</p>
        </div>
      </div>
    </router-link>

    <ul class="flex items-center gap-10">
      <li
        @click="() => emit('openDrawer')"
        class="flex items-center cursor-pointer gap-3 text-gray-500 hover:text-black"
      >
        <img :src="getImageUrl('/cart.svg')" alt="Cart" />
        <b>{{ totalPrice }} руб.</b>
      </li>

      <router-link to="/favorites">
        <li class="flex items-center cursor-pointer gap-3 text-gray-500 hover:text-black">
          <img :src="getImageUrl('/heart.svg')" alt="Cart" />
          <span>Закладки</span>
        </li>
      </router-link>

      <li class="flex items-center cursor-pointer gap-3 text-gray-500 hover:text-black">
        <img :src="getImageUrl('/profile.svg')" alt="Cart" />
        <span>Профиль</span>
      </li>
    </ul>
  </header>
</template>

<script setup>
// Функция для правильного формирования путей к изображениям
const getImageUrl = (path) => {
  const baseUrl = import.meta.env.BASE_URL || '/'
  return baseUrl + path.replace(/^\//, '')
}

const emit = defineEmits(['openDrawer'])

defineProps({
  totalPrice: Number,
})
</script>
