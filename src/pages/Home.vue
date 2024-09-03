<script setup lang="ts">
import { reactive, watch, ref, onMounted } from 'vue'
import axios from 'axios'
import debounce from 'lodash.debounce'
import { inject } from 'vue'
import CardList from '../components/CardList.vue'

interface Item {
  id: number
  title: string
  imageUrl: string
  price: number
  isFavorite: boolean
  favoriteId: number | null
  isAdded: boolean
}

interface Filters {
  sortBy: string
  searchQuery: string
}

const apiUrl = ref<string>(import.meta.env.VITE_APP_API_URL)

const { cart, addToCart, removeFromCart } = inject<{
  cart: { value: Item[] }
  addToCart: (item: Item) => void
  removeFromCart: (item: Item) => void
}>('cart') || {
  cart: ref<Item[]>([]),
  addToCart: () => {},
  removeFromCart: () => {}
}

const items = ref<Item[]>([])

const filters = reactive<Filters>({
  sortBy: 'title',
  searchQuery: ''
})

const onClickAddPlus = (item: Item) => {
  if (!item.isAdded) {
    addToCart(item)
  } else {
    removeFromCart(item)
  }
}

const onChangeSelect = (event: Event) => {
  filters.sortBy = (event.target as HTMLSelectElement).value
}

const onChangeSearchInput = debounce((event: Event) => {
  filters.searchQuery = (event.target as HTMLInputElement).value
}, 300)

const addToFavorite = async (item: Item) => {
  try {
    if (!item.isFavorite) {
      const obj = { sneaker_id: item.id }

      item.isFavorite = true

      const { data } = await axios.post<{ id: number }>(`${apiUrl.value}/favorites`, obj)

      item.favoriteId = data.id
    } else {
      if (item.favoriteId !== null) {
        item.isFavorite = false
        await axios.delete(`${apiUrl.value}/favorites/${item.favoriteId}`)
        item.favoriteId = null
      }
    }
  } catch (err) {
    console.error('Error adding/removing favorite:', err)
  }
}

const fetchFavorites = async () => {
  try {
    const { data: favorites } = await axios.get<{ sneaker_id: number; id: number }[]>(
      `${apiUrl.value}/favorites`
    )

    items.value = items.value.map((item) => {
      const favorite = favorites.find((fav) => fav.sneaker_id === item.id)

      return favorite ? { ...item, isFavorite: true, favoriteId: favorite.id } : item
    })
  } catch (err) {
    console.error('Error fetching favorites:', err)
  }
}

const fetchItems = async () => {
  try {
    const { data } = await axios.get<
      { id: number; title: string; imageUrl: string; price: number }[]
    >(`${apiUrl.value}/sneakers`)

    items.value = data.map((obj) => ({
      id: obj.id,
      title: obj.title,
      imageUrl: obj.imageUrl,
      price: obj.price,
      isFavorite: false,
      favoriteId: null,
      isAdded: false
    }))
  } catch (err) {
    console.error('Error fetching items:', err)
  }
}

onMounted(async () => {
  const localCart = localStorage.getItem('cart')
  cart.value = localCart ? JSON.parse(localCart) : []

  await fetchItems()
  await fetchFavorites()

  items.value = items.value.map((item) => ({
    ...item,
    isAdded: cart.value.some((cartItem) => cartItem.id === item.id)
  }))
})

watch(cart, (newCart) => {
  localStorage.setItem('cart', JSON.stringify(newCart))
  items.value = items.value.map((item) => ({
    ...item,
    isAdded: (cart.value as Item[]).some((cartItem) => cartItem.id === item.id)
  }))
})

watch(cart, (newCart) => {
  localStorage.setItem('cart', JSON.stringify(newCart))
  items.value = items.value.map((item) => ({
    ...item, // spread the existing properties
    isAdded: (cart.value as Item[]).some((cartItem) => cartItem.id === item.id)
  }))
})
</script>

<template>
  <div class="flex flex-wrap justify-between items-center">
    <h2 class="text-3xl font-bold mb-8">Все кроссовки</h2>

    <div class="flex gap-4">
      <select @change="onChangeSelect" class="py-2 px-3 border rounded-md outline-none">
        <option value="name">По названию</option>
        <option value="price">По цене (дешевые)</option>
        <option value="-price">По цене (дорогие)</option>
      </select>

      <div class="relative">
        <img class="absolute left-4 top-3" src="/search.svg" />
        <input
          @input="onChangeSearchInput"
          class="border rounded-md py-2 pl-11 pr-4 outline-none focus:border-gray-400"
          type="text"
          placeholder="Поиск..."
        />
      </div>
    </div>
  </div>

  <div class="mt-10">
    <CardList :items="items" @add-to-favorite="addToFavorite" @add-to-cart="onClickAddPlus" />
  </div>
</template>
