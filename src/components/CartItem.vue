<script setup lang="ts">
import { defineProps, defineEmits } from 'vue'

interface Props {
  id: number
  title: string
  imageUrl: string
  price: number
}

const emit = defineEmits(['onClickRemove'])

const props = defineProps<Props>()
</script>

<template>
  <div class="flex items-center border border-slate-200 p-4 rounded-xl gap-4">
    <img class="w-16 h-16" :src="props.imageUrl" :alt="props.title" />

    <div class="flex flex-col flex-1">
      <p>{{ props.title }}</p>

      <div class="flex justify-between mt-2">
        <b class="flex-1">{{ props.price }} руб.</b>
        <img
          @click="emit('onClickRemove')"
          class="opacity-40 hover:opacity-100 cursor-pointer transition"
          src="/close.svg"
        />
      </div>
    </div>
  </div>
</template>
