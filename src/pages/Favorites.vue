<template>
  <h2 class="text-3xl font-bold mb-8">Мои закладки</h2>

  <CardList :items="favorites" is-favorites />
</template>

<script setup>
import { onMounted, ref } from 'vue'
import axios from 'axios'
import CardList from '../components/CardList.vue'

const favorites = ref([])

onMounted(async () => {
  try {
    const { data } = await axios.get('https://d8767a27d59da728.mokky.dev/favorites')
    console.log(data)
    favorites.value = data.map((obj) => obj.item)
  } catch (error) {
    console.log(error)
  }
})
</script>
