<script setup lang="ts">
interface Props {
  title: string
  description: string
  imageUrl: string
}

const props = defineProps<Props>()
</script>

<template>
  <div class="flex flex-col items-center text-center w-72 mx-auto">
    <img height="80" width="80" :src="props.imageUrl" alt="Info image" />
    <h2 class="mt-4 text-2xl font-medium">{{ props.title }}</h2>
    <p class="text-gray-400 mt-2">{{ props.description }}</p>
  </div>
</template>
