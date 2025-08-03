<template>
  <div class="flex justify-between items-center">
    <h2 class="text-3xl font-bold mb-8">Кроссовки</h2>
    <div class="flex gap-4">
      <select
        @change="onChangeSelect"
        class="py-2 px-3 border border-gray-300 rounded outline-none"
      >
        <option value="title">По названию</option>
        <option value="price">По цене (дешевые)</option>
        <option value="-price">По цене (дорогие)</option>
      </select>
      <div class="relative">
        <img class="absolute left-3 top-3" src="/search.svg" alt="Search" />
        <input
          @input="onChangeSearchInput"
          class="border border-gray-300 rounded-md py-2 pl-11 pr-4 outline-none focus:border-gray-400"
          placeholder="Поиск..."
          type="text"
        />
      </div>
    </div>
  </div>
  <div class="mt-10">
    <CardList :items="items" @add-to-favorite="addToFavorite" @add-to-cart="onClickAddPlus" />
  </div>
</template>

<script setup>
import axios from 'axios'
import debounce from 'lodash.debounce'
import CardList from '../components/CardList.vue'
import { onMounted, reactive, ref, watch, inject } from 'vue'

// Моковые данные для тестирования
const mockItems = [
  {
    id: 1,
    title: 'Кроссовки Nike Air Max',
    price: 12900,
    imageUrl: '/sneakers/sneakers-1.jpg',
    isFavorite: false,
    favoriteId: null,
    isAdded: false,
  },
  {
    id: 2,
    title: 'Кроссовки Adidas Ultraboost',
    price: 15900,
    imageUrl: '/sneakers/sneakers-2.jpg',
    isFavorite: false,
    favoriteId: null,
    isAdded: false,
  },
  {
    id: 3,
    title: 'Кроссовки Puma RS-X',
    price: 8900,
    imageUrl: '/sneakers/sneakers-3.jpg',
    isFavorite: false,
    favoriteId: null,
    isAdded: false,
  },
  {
    id: 4,
    title: 'Кроссовки New Balance',
    price: 11900,
    imageUrl: '/sneakers/sneakers-4.jpg',
    isFavorite: false,
    favoriteId: null,
    isAdded: false,
  },
  {
    id: 5,
    title: 'Кроссовки Reebok Classic',
    price: 9900,
    imageUrl: '/sneakers/sneakers-5.jpg',
    isFavorite: false,
    favoriteId: null,
    isAdded: false,
  },
]

const items = ref([])

const { cart, addToCart, removeFromCart } = inject('cart')

const filters = reactive({
  sortBy: 'title',
  searchQuery: '',
})

// Функция для правильного формирования путей к изображениям
const getImageUrl = (path) => {
  const baseUrl = import.meta.env.BASE_URL || '/'
  return baseUrl + path.replace(/^\//, '')
}

const onClickAddPlus = (item) => {
  if (!item.isAdded) {
    addToCart(item)
  } else {
    removeFromCart(item)
  }
}

const onChangeSelect = (event) => {
  filters.sortBy = event.target.value
}

const onChangeSearchInput = debounce((event) => {
  filters.searchQuery = event.target.value
}, 300)

const addToFavorite = async (item) => {
  try {
    if (!item.isFavorite) {
      const obj = {
        parentId: item.id,
        item,
      }
      item.isFavorite = true

      const { data } = await axios.post('https://d8767a27d59da728.mokky.dev/favorites', obj)

      item.favoriteId = data.id
    } else {
      item.isFavorite = false

      await axios.delete(`https://d8767a27d59da728.mokky.dev/favorites/${item.favoriteId}`)

      item.favoriteId = null
    }
  } catch (error) {
    console.log('Error with favorites:', error)
    // Fallback для тестирования
    item.isFavorite = !item.isFavorite
  }
}

const fetchFavorites = async () => {
  try {
    const { data: favorites } = await axios.get('https://d8767a27d59da728.mokky.dev/favorites')
    items.value = items.value.map((item) => {
      const favorite = favorites.find((favorite) => favorite.parentId === item.id)

      if (!favorite) {
        return item
      }

      return {
        ...item,
        isFavorite: true,
        favoriteId: favorite.id,
      }
    })
  } catch (error) {
    console.log('Error fetching favorites:', error)
  }
}

const fetchItems = async () => {
  try {
    const params = {
      sortBy: filters.sortBy,
    }

    if (filters.searchQuery) {
      params.title = `*${filters.searchQuery}*`
    }

    const { data } = await axios.get('https://d8767a27d59da728.mokky.dev/items', {
      params,
    })
    items.value = data.map((obj) => ({
      ...obj,
      imageUrl: getImageUrl(obj.imageUrl), // Используем правильный путь
      isFavorite: false,
      favoriteId: null,
      isAdded: false,
    }))
  } catch (error) {
    console.log('Error fetching items:', error)
    // Используем моковые данные при ошибке
    items.value = mockItems.map((item) => ({
      ...item,
      imageUrl: getImageUrl(item.imageUrl),
    }))
  }
}

onMounted(async () => {
  const localCart = localStorage.getItem('cart')
  cart.value = localCart ? JSON.parse(localCart) : []

  // Сначала попробуем загрузить реальные данные
  try {
    await fetchItems()
    await fetchFavorites()
  } catch (error) {
    console.log('Using mock data due to API error:', error)
    // Используем правильные пути для моковых данных
    items.value = mockItems.map((item) => ({
      ...item,
      imageUrl: getImageUrl(item.imageUrl),
    }))
  }

  items.value = items.value.map((item) => ({
    ...item,
    isAdded: cart.value.some((cartItem) => cartItem.id === item.id),
  }))
})

watch(filters, fetchItems)

watch(cart, () => {
  items.value = items.value.map((item) => ({
    ...item,
    isAdded: false,
  }))
})
</script>
