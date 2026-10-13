<script setup lang="ts">
import { ref } from 'vue'
import DogCard from './components/DogCard.vue'
// apiから帰ってくる犬画像
type DogApiResponse = {
  message: string
  status: string
}

// 表示する犬画像
type DogImage = {
  url: string
  isLiked: boolean
}
const dogImageUrls = ref<DogImage[]>([])

const isLoading = ref(false)
const errorMessage = ref('')
// 画像取得
async function getDogImage() {
  try {
    if (isLoading.value) {
      return
    }
    errorMessage.value = ''
    isLoading.value = true
    const response = await fetch('https://dog.ceo/api/breeds/image/random')

    if (!response.ok) {
      throw new Error('データの取得に失敗しました。')
    }
    const dog: DogApiResponse = await response.json()
    dogImageUrls.value.push({ url: dog.message, isLiked: false })
  } catch (error) {
    // エラーオブジェクトの場合
    if (error instanceof Error) {
      errorMessage.value = error.message
    } else {
      // エラーオブジェクト以外
      errorMessage.value = '画像取得に失敗しました。'
    }
    console.error(error)
  } finally {
    isLoading.value = false
  }
}
// 画像削除
function deleteDog(dogImageUrl: DogImage) {
  dogImageUrls.value = dogImageUrls.value.filter((image) => {
    return image.url !== dogImageUrl.url
  })
}
function toggleLikeButton(dogImageUrl: DogImage) {
  dogImageUrl.isLiked = !dogImageUrl.isLiked
}
</script>

<template>
  <header class="page-header">
    <p v-if="errorMessage" class="error-message">{{ errorMessage }}</p>

    <h1 class="center">Dog Gallery</h1>
    <p class="center">お気に入りのわんこを見つけよう</p>
  </header>

  <main class="container">
    <div class="d-flex w-100">
      <DogCard :dog-image-urls="dogImageUrls" @toggle-like="toggleLikeButton" @delete="deleteDog" />
    </div>

    <!-- 読み込み中表示 -->
    <div v-if="isLoading" class="loading">
      <div class="spinner"></div>
      <p v-if="isLoading">読み込み中...</p>
    </div>

    <button class="mt-10" :disabled="isLoading" @click="getDogImage">新しい犬の画像を取得</button>
  </main>
</template>

<style>
.page-header {
  text-align: center;
  line-height: 1.5;
  margin-bottom: 30px;
}
.page-header h1 {
  margin-top: 0;
}
.container {
  width: 980px;
  max-width: 100%;
  margin: 0 auto;
}
header {
  line-height: 1.5;
  margin-bottom: 30px;
}
hr {
  border: none;
  border-top: 1px solid #ddd;
}
.center {
  text-align: center;
}
.d-flex {
  display: flex;
  flex-wrap: wrap;
  gap: 20px;
}
.mt-10 {
  margin-top: 10px;
}
.w-100 {
  width: 100%;
}
.w-50 {
  width: calc(50% - 10px);
  box-sizing: border-box;
}

.w-50 img {
  width: 100%;
  height: 400px;
  object-fit: cover;
  display: block;
}

/* 読み込み中表示 */
.loading {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 10px;
  padding: 20px;
}
.spinner {
  width: 40px;
  height: 40px;
  border: 4px solid #ddd;
  border-top-color: #42b883;
  border-radius: 50%;
  animation: spin 0.8s linear infinite;
}
@keyframes spin {
  to {
    transform: rotate(360deg);
  }
}
.error-message {
  color: #dc2626;
  text-align: center;
  padding: 10px;
  border: #dc2626 1px solid;
  border-radius: 8px;
}
/* お気に入りボタン */
.heart-button {
  width: 44px;
  height: 44px;
  padding: 0;
  border: none;
  background: transparent;
  color: #ccc;
  font-size: 32px;
  line-height: 1;
  cursor: pointer;
}
.heart-button.liked {
  color: #e11d48;
}
</style>
