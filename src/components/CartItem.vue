<template>
  <div class="flex items-center border border-slate-100 p-4 rounded-xl gap-4">
    <img class="w-16 h-16" :src="imageUrl" :alt="title" />
    <div class="flex flex-col flex-1">
      <p>{{ title }}</p>
      <div class="flex justify-between mt-2">
        <b class="flex-1">{{ price }} руб.</b>
        <img
          @click="emit('onClickRemove')"
          class="opacity-40 hover:opacity-100 cursor-pointer transition"
          :src="getImageUrl('/close.svg')"
          alt=""
        />
      </div>
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
})

const emit = defineEmits(['onClickRemove'])
</script>
