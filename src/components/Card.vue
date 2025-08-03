<template>
  <div
    class="relative bg-white border border-slate-100 rounded-3xl p-8 cursor-pointer transition hover:-translate-y-2 hover:shadow-xl"
  >
    <img
      v-if="onClickFavorite"
      class="absolute top-8 left-8"
      :src="isFavorite ? getImageUrl('/like-2.svg') : getImageUrl('/like-1.svg')"
      alt="Like 1"
      @click="onClickFavorite"
    />
    <img :src="imageUrl" alt="Sneaker" />
    <p class="mt-2">{{ title }}</p>
    <div class="flex justify-between mt-5">
      <div class="flex flex-col">
        <span class="text-slate-400">Цена:</span>
        <b>{{ price }} руб.</b>
      </div>
      <img
        v-if="onClickAdd"
        @click="onClickAdd"
        :src="!isAdded ? getImageUrl('/plus.svg') : getImageUrl('/checked.svg')"
        alt="Plus"
      />
    </div>
  </div>
</template>

<script setup>
// Функция для правильного формирования путей к изображениям
const getImageUrl = (path) => {
  const baseUrl = import.meta.env.BASE_URL || '/'
  return baseUrl + path.replace(/^\//, '')
}

defineProps({
  id: Number,
  imageUrl: String,
  title: String,
  price: Number,
  isFavorite: Boolean,
  isAdded: Boolean,
  onClickFavorite: Function,
  onClickAdd: Function,
})
</script>

