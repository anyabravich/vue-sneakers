<script setup lang="ts">
import Card from './Card.vue'
import { defineProps, defineEmits } from 'vue'

interface Item {
  id: number
  title: string
  imageUrl: string
  price: number
  isFavorite: boolean
  isAdded: boolean
}

const Props = defineProps({
  items: Array as () => Item[],
  isFavorites: Boolean
})

const emit = defineEmits(['addToFavorite', 'addToCart'])
</script>

<template>
  <div class="grid md:grid-cols-2 xl:grid-cols-4 gap-5" v-auto-animate>
    <Card
      v-for="item in Props.items"
      :key="item.id"
      :id="item.id"
      :title="item.title"
      :imageUrl="item.imageUrl"
      :price="item.price"
      :onClickFavorite="Props.isFavorites ? () => {} : () => emit('addToFavorite', item)"
      :onClickAdd="Props.isFavorites ? () => {} : () => emit('addToCart', item)"
      :isFavorite="item.isFavorite"
      :isAdded="item.isAdded"
    />
  </div>
</template>
