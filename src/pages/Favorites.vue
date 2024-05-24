<script setup lang="ts">
import { ref, onMounted } from 'vue'
import axios from 'axios'

const apiUrl = ref(import.meta.env.VITE_APP_API_URL).value
import CardList from '../components/CardList.vue'

const favorites = ref([])

interface Sneaker {
  id: number
  title: string
  imageUrl: string
  price: number
}

onMounted(async () => {
  try {
    const { data } = await axios.get(`${apiUrl}/favorites?_relations=sneakers`)

    favorites.value = data.map((obj: { sneaker: Sneaker }) => obj.sneaker)
  } catch (err) {
    console.log(err)
  }
})
</script>

<template>
  <h2 class="text-3xl font-bold mb-8">Мои закладки</h2>

  <CardList :items="favorites" is-favorites />
</template>
