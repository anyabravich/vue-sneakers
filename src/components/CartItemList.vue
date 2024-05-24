<script setup lang="ts">
import { inject } from 'vue'
import CartItem from './CartItem.vue'

interface CartItem {
  id: number
  title: string
  price: number
  imageUrl: string
}

const cartContext = inject<{ cart: CartItem[]; removeFromCart: (item: CartItem) => void }>('cart')

if (!cartContext) {
  throw new Error('Unable to inject cart context')
}

const { cart, removeFromCart } = cartContext
</script>

<template>
  <div class="flex flex-col flex-1 gap-4 justify-between" v-auto-animate>
    <CartItem
      v-for="item in cart"
      :key="item.id"
      :title="item.title"
      :price="item.price"
      :image-url="item.imageUrl"
      @on-click-remove="() => removeFromCart(item)"
      :id="item.id"
    />
  </div>
</template>
