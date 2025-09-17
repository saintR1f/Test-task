<script setup>
import FullPost from './FullPost.vue'
import Item from './Item.vue'

import { ref, computed, onMounted, provide, watch } from 'vue'

const items = ref([])
const currentPost = ref(null)
const postOpend = ref(false)
const inputData = ref()

const closeFullPost = () => {
  postOpend.value = false
}

const openFullPost = async (postId) => {
  const postData = await fullFetchItemsByPostId(postId)

  if (postData) {
    currentPost.value = postData
    postOpend.value = true
  }
}

const fullFetchItemsByInput = async (inputString) => {
  const url = `https://dummyjson.com/posts/search?q=${inputString}`
  try {
    const response = await fetch(url)
    if (!response.ok) {
      throw new Error(response.status)
    }

    const data = await response.json()
    items.value = data.posts
  } catch (error) {
    console.error(error.message)
  }
}

const fullFetchItemsByPostId = async (id) => {
  const storageKey = id

  const cache = getItemsFromStorage(storageKey)
  if (cache) {
    return cache
  }

  const url = `https://dummyjson.com/posts/${id}`
  try {
    const response = await fetch(url)
    if (!response.ok) {
      throw new Error(response.status)
    }

    const data = await response.json()
    saveToStorage(storageKey, data)
    return data
  } catch (error) {
    console.error(error.message)
  }
}

const saveToStorage = (key, data) => {
  try {
    sessionStorage.setItem(key, JSON.stringify(data))
  } catch (error) {
    console.log('Saving error')
  }
}

const getItemsFromStorage = (key) => {
  try {
    const item = sessionStorage.getItem(key)
    return item ? JSON.parse(item) : null
  } catch (error) {
    console.log('Getting error')
  }
}

const fetchItems = async () => {
  const storageKey = 'posts_list'

  const cache = getItemsFromStorage(storageKey)
  if (cache) {
    items.value = cache
    return
  }

  const url = 'https://dummyjson.com/posts'
  try {
    const response = await fetch(url)
    if (!response.ok) {
      throw new Error(response.status)
    }

    const data = await response.json()
    items.value = data.posts

    saveToStorage(storageKey, data.posts)
  } catch (error) {
    console.error(error.message)
  }
}

const updatePostInList = (updatedPost) => {
  const index = items.value.findIndex((item) => item.id === updatedPost.id)
  items.value[index] = { ...items.value[index], ...updatedPost }

  const storageKey = items.value[index].id
  saveToStorage(storageKey, items.value[index])

  const storageKey2 = 'posts_list'
  saveToStorage(storageKey2, items.value)
}

watch(
  () => inputData.value,
  (newInput) => fullFetchItemsByInput(newInput),
)

onMounted(async () => {
  await fetchItems()
})
provide('Actions', { closeFullPost, openFullPost, updatePostInList })
</script>

<template>
  <FullPost v-if="postOpend" :post-data="currentPost" />
  <div class="main">
    <div class="body">
      <div class="main-header">
        <div class="header-text">
          <h1>Коллекция постов</h1>
          <p>Разнообрзные крутые и захватывающие новости от людей из разных сфер</p>
        </div>
        <div class="input-header">
          <input type="text" v-model="inputData" placeholder="Поиск по содержанию и названию" /><svg
            viewBox="0 0 24 24"
            fill="none"
            xmlns="http://www.w3.org/2000/svg"
          >
            <g id="SVGRepo_bgCarrier" stroke-width="0"></g>
            <g id="SVGRepo_tracerCarrier" stroke-linecap="round" stroke-linejoin="round"></g>
            <g id="SVGRepo_iconCarrier">
              <path
                d="M20 20L15.8033 15.8033C15.8033 15.8033 14 18 10.5 18C6.35786 18 3 14.6421 3 10.5C3 6.35786 6.35786 3 10.5 3C14.6421 3 18 6.35786 18 10.5C18 11.0137 17.9484 11.5153 17.85 12"
                stroke="#000000"
                stroke-width="1.5"
                stroke-linecap="round"
                stroke-linejoin="round"
              ></path>
            </g>
          </svg>
        </div>
      </div>

      <div class="list-grid">
        <Item
          v-for="item in items"
          :key="item.id"
          :post-title="item.title"
          :post-body="item.body.length > 100 ? item.body.slice(0, 100) + '...' : item.body"
          :post-tags="item.tags.join(' ; ')"
          :post-likes="item.reactions.likes"
          :post-dislikes="item.reactions.dislikes"
          :post-views="item.views"
          :post-user-id="item.userId"
          :post-id="item.id"
        />
      </div>
    </div>
  </div>
</template>

<style scoped>
.input-header {
  margin-right: 100px;
  display: flex;
  background: white;
  border-radius: 15px;
  width: 400px;
}
.input-header > svg {
  width: 30px;
  margin-right: 20px;
  cursor: pointer;
}
input:focus {
  outline: none;
  box-shadow: none;
}
.input-header > input {
  width: 350px;
  height: 42px;
  border-style: none;
  padding-left: 10px;
  margin-left: 20px;
}
.main-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-left: 20px;
  padding-bottom: 20px;
}
.body {
  width: 1440px;
  margin: auto;
}
.list-grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
}
.main {
  width: 100%;
  height: auto;
  min-height: 100vh;
  background: rgb(18, 32, 51);
}
h1,
p {
  color: white;
}
@media (max-width: 1028px) {
  .body {
    width: 980px;
  }
  .list-grid {
    grid-template-columns: repeat(2, 1fr);
  }
}
@media (max-width: 750px) {
  .body {
    width: 500px;
  }
  .list-grid {
    grid-template-columns: repeat(1, 1fr);
  }
}
</style>
