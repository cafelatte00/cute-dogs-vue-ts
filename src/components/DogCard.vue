<script setup lang="ts">
// 表示する犬画像
type DogImage = {
  url: string
  isLiked: boolean
}
defineProps<{
  dogImageUrls: DogImage[]
}>()

const emit = defineEmits<{
  // イベント名: [渡すデータの引数名:渡すデータ型]
  toggleLike: [dogImageUrl: DogImage]
  delete: [dogImageUrl: DogImage]
}>()
</script>
<template>
  <div v-for="dogImageUrl in dogImageUrls" :key="dogImageUrl.url" class="w-50">
    <img :src="dogImageUrl.url" />
    <button
      class="heart-button"
      :class="{ liked: dogImageUrl.isLiked }"
      @click="emit('toggleLike', dogImageUrl)"
    >
      ♥
    </button>
    <button @click="emit('delete', dogImageUrl)">削除</button>
  </div>
</template>
