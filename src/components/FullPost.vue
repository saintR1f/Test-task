<script setup>
import { inject, onMounted, onUnmounted, ref, watch, computed, provide } from 'vue'
const userInfo = ref([])
const comments = ref([])
const isEditing = ref(false)
const editedTitle = ref('')
const editedBody = ref('')
const showButton = ref(false)

const { closeFullPost, updatePostInList, openFullPostById } = inject('Actions')
const props = defineProps({
  postData: Object,
})

const currentPostId = computed(() => props.postData.posts[0].id)

const post = computed(() => {
  if (!props.postData || !props.postData.posts) return null
  return props.postData.posts[0]
})

const postId = computed(() => post.value.id)
const postTitle = computed(() => post.value.title)
const postBody = computed(() => post.value.body)
const postUserId = computed(() => post.value.userId)
const postTags = computed(() => post.value.tags.join(' , '))

const authorName = computed(() => userInfo.value.firstName)
const authorLastName = computed(() => userInfo.value.lastName)
const authorMaidenName = computed(() => userInfo.value.maidenName)
const authorJob = computed(() => `Вакансия: ${userInfo.value.company.title}`)
const authorDepartment = computed(() => `Отдел: ${userInfo.value.company.department}`)

const commentsInfo = computed(() => comments.value.comments)

const getNextPostId = (currentId) => {
  if (currentId >= 30) return 30
  return currentId + 1
}

const getPrevPostId = (currentId) => {
  if (currentId <= 1) return 1
  return currentId - 1
}

const loadNextPost = () => {
  const nextId = getNextPostId(currentPostId.value)
  console.log(nextId)
  if (nextId !== currentPostId.value) {
    openFullPostById(nextId)
  }
}

const loadPrevPost = () => {
  const prevId = getPrevPostId(currentPostId.value)
  if (prevId !== currentPostId.value) {
    openFullPostById(prevId)
  }
}

const saveChanges = async () => {
  try {
    const response = await fetch(`https://dummyjson.com/posts/${postId.value}`, {
      method: 'PATCH',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({
        title: editedTitle.value,
        body: editedBody.value,
      }),
    })
    if (!response.ok) {
      throw new Error(response.status)
    }

    const updatedPost = await response.json()

    if (post.value) {
      post.value.title = updatedPost.title
      post.value.body = updatedPost.body
    }

    isEditing.value = false
    showButton.value = false
  } catch (error) {
    console.error(error.message)
  }
}
const checkChanges = () => {
  if (isEditing.value) {
    showButton.value = editedTitle.value !== postTitle.value || editedBody.value !== postBody.value
  }
}
const cancelEditing = () => {
  isEditing.value = false
  showButton.value = false
  editedTitle.value = postTitle.value
  editedBody.value = postBody.value
}
const enableEditing = () => {
  isEditing.value = true
  editedTitle.value = postTitle.value
  editedBody.value = postBody.value
  showButton.value = false
}
const fetchUser = async (userIId) => {
  const url = `https://dummyjson.com/users/${userIId}`
  try {
    const response = await fetch(url)
    if (!response.ok) {
      throw new Error(`Response: ${response.status}`)
    }
    const data = await response.json()
    userInfo.value = data
  } catch (error) {
    console.log(error.massage)
  }
}
const fetchComments = async (postIId) => {
  const url = `https://dummyjson.com/posts/${postIId}/comments`
  try {
    const response = await fetch(url)
    if (!response.ok) {
      throw new Error(`Response: ${response.status}`)
    }
    const data = await response.json()
    comments.value = data
  } catch (error) {
    console.log(error.massage)
  }
}

onMounted(() => {
  document.body.style.overflow = 'hidden'
  document.body.style.paddingRight = '15px'
  document.body.style.background = 'rgb(18, 32, 51)'
})
watch(
  () => postUserId.value,
  (nemUserid) => {
    fetchUser(nemUserid)
  },
  { immediate: true },
)
watch(
  () => postId.value,
  (newPostid) => {
    fetchComments(newPostid)
  },
  { immediate: true },
)
watch(
  () => postId.value,
  (newPostId, oldPostId) => {
    if (newPostId !== oldPostId) {
      isEditing.value = false
      showButton.value = false
      editedTitle.value = postTitle.value
      editedBody.value = postBody.value
    }
  },
)

watch([() => editedTitle.value, () => editedBody.value], () => {
  checkChanges()
})

onUnmounted(() => {
  document.body.style.overflow = ''
  document.body.style.paddingRight = '0px'
})
</script>

<template>
  <div class="post-back">
    <div class="post">
      <div class="post-in">
        <div class="post-header">
          <input
            v-if="isEditing"
            v-model="editedTitle"
            @input="checkChanges"
            class="title-input"
            placeholder="Заголовок поста"
          />
          <p v-else class="post-title">{{ postTitle }}</p>
          <svg
            @click="closeFullPost"
            viewBox="0 0 24 24"
            fill="none"
            xmlns="http://www.w3.org/2000/svg"
          >
            <g id="SVGRepo_bgCarrier" stroke-width="0"></g>
            <g id="SVGRepo_tracerCarrier" stroke-linecap="round" stroke-linejoin="round"></g>
            <g id="SVGRepo_iconCarrier">
              <path
                d="M16 8L8 16M12 12L16 16M8 8L10 10M21 12C21 16.9706 16.9706 21 12 21C7.02944 21 3 16.9706 3 12C3 7.02944 7.02944 3 12 3C16.9706 3 21 7.02944 21 12Z"
                stroke="#000000"
                stroke-width="1.5"
                stroke-linecap="round"
                stroke-linejoin="round"
              ></path>
            </g>
          </svg>
        </div>

        <div class="two-blocks">
          <div class="left-block">
            <div class="author-info">
              <div class="author-name">
                <svg viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
                  <g id="SVGRepo_bgCarrier" stroke-width="0"></g>
                  <g id="SVGRepo_tracerCarrier" stroke-linecap="round" stroke-linejoin="round"></g>
                  <g id="SVGRepo_iconCarrier">
                    <path
                      d="M3.95442 10.166C4.04608 9.76202 3.79293 9.36025 3.38898 9.26859C2.98504 9.17693 2.58327 9.43009 2.49161 9.83403L3.95442 10.166ZM5.49981 4.73283C5.19117 5.00907 5.1649 5.48322 5.44115 5.79187C5.71739 6.10051 6.19154 6.12678 6.50019 5.85053L5.49981 4.73283ZM15 14.25C14.5858 14.25 14.25 14.5858 14.25 15C14.25 15.4142 14.5858 15.75 15 15.75L15 14.25ZM17.25 18.7083C17.25 19.1225 17.5858 19.4583 18 19.4583C18.4142 19.4583 18.75 19.1225 18.75 18.7083H17.25ZM5.25 18.7083C5.25 19.1225 5.58579 19.4583 6 19.4583C6.41421 19.4583 6.75 19.1225 6.75 18.7083H5.25ZM9 15L8.99998 15.75H9V15ZM11 15.75C11.4142 15.75 11.75 15.4142 11.75 15C11.75 14.5858 11.4142 14.25 11 14.25V15.75ZM12 3.75C16.5563 3.75 20.25 7.44365 20.25 12H21.75C21.75 6.61522 17.3848 2.25 12 2.25V3.75ZM12 20.25C7.44365 20.25 3.75 16.5563 3.75 12H2.25C2.25 17.3848 6.61522 21.75 12 21.75V20.25ZM20.25 12C20.25 16.5563 16.5563 20.25 12 20.25V21.75C17.3848 21.75 21.75 17.3848 21.75 12H20.25ZM3.75 12C3.75 11.3688 3.82074 10.7551 3.95442 10.166L2.49161 9.83403C2.33338 10.5313 2.25 11.2564 2.25 12H3.75ZM6.50019 5.85053C7.96026 4.54373 9.88655 3.75 12 3.75V2.25C9.50333 2.25 7.22428 3.1894 5.49981 4.73283L6.50019 5.85053ZM14.25 9C14.25 10.2426 13.2426 11.25 12 11.25V12.75C14.0711 12.75 15.75 11.0711 15.75 9H14.25ZM12 11.25C10.7574 11.25 9.75 10.2426 9.75 9H8.25C8.25 11.0711 9.92893 12.75 12 12.75V11.25ZM9.75 9C9.75 7.75736 10.7574 6.75 12 6.75V5.25C9.92893 5.25 8.25 6.92893 8.25 9H9.75ZM12 6.75C13.2426 6.75 14.25 7.75736 14.25 9H15.75C15.75 6.92893 14.0711 5.25 12 5.25V6.75ZM15 15.75C15.6008 15.75 16.1482 16.0891 16.5769 16.6848C17.0089 17.2852 17.25 18.0598 17.25 18.7083H18.75C18.75 17.7371 18.4052 16.6575 17.7944 15.8086C17.1801 14.9551 16.2275 14.25 15 14.25L15 15.75ZM6.75 18.7083C6.75 18.0598 6.99109 17.2852 7.42315 16.6848C7.85183 16.0891 8.39919 15.75 8.99998 15.75L9.00002 14.25C7.77253 14.25 6.81989 14.9551 6.20564 15.8086C5.59477 16.6575 5.25 17.7371 5.25 18.7083H6.75ZM9 15.75H11V14.25H9V15.75Z"
                      fill="#000000"
                    ></path>
                  </g>
                </svg>
                <p>Автор:</p>
                <p>{{ authorName }}</p>
                <p>{{ authorLastName }}</p>
                <p>{{ authorMaidenName }}</p>
              </div>

              <div class="author-job">
                <p>{{ authorJob }}</p>
                <p>{{ authorDepartment }}</p>
              </div>
            </div>

            <details class="comments">
              <summary class="comments-title">Комментарии ({{ commentsInfo.length }})</summary>
              <div v-for="comment in commentsInfo" :key="comment.id" class="comments-content">
                <div class="com-inc">
                  <svg viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
                    <g id="SVGRepo_bgCarrier" stroke-width="0"></g>
                    <g
                      id="SVGRepo_tracerCarrier"
                      stroke-linecap="round"
                      stroke-linejoin="round"
                    ></g>
                    <g id="SVGRepo_iconCarrier">
                      <path
                        d="M8 15C5.79086 15 4 16.7909 4 19V21H20V19C20 16.7909 18.2091 15 16 15H12M12 3C9.79086 3 8 4.79086 8 7C8 9.20914 9.79086 11 12 11C14.2091 11 16 9.20914 16 7C16 6.27143 15.8052 5.58835 15.4649 5"
                        stroke="#000000"
                        stroke-width="1.5"
                        stroke-linecap="round"
                        stroke-linejoin="round"
                      ></path>
                    </g>
                  </svg>
                  <p class="comment-author">- {{ comment.user.username }}</p>
                </div>
                <p class="comment-body">{{ comment.body }}</p>
              </div>
              <p v-if="commentsInfo.length === 0">Комментариев нет</p>
            </details>

            <div class="tags">
              <svg viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
                <g id="SVGRepo_bgCarrier" stroke-width="0"></g>
                <g id="SVGRepo_tracerCarrier" stroke-linecap="round" stroke-linejoin="round"></g>
                <g id="SVGRepo_iconCarrier">
                  <path
                    d="M4 9V4H20L15 9L20 14H4V21"
                    stroke="#000000"
                    stroke-width="1.5"
                    stroke-linecap="round"
                    stroke-linejoin="round"
                  ></path>
                </g>
              </svg>
              <p class="tags-content">{{ postTags }}</p>
            </div>
          </div>

          <div class="right-block">
            <p class="post-content-title">Новость.</p>
            <textarea
              v-if="isEditing"
              v-model="editedBody"
              @input="checkChanges"
              class="edit-body-textarea"
              placeholder="Содержимое поста"
              rows="8"
            />
            <p v-else class="post-content">{{ postBody }}</p>
            <div class="edit">
              <button v-if="!isEditing" @click="enableEditing" class="edit-button">
                <svg viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
                  <g id="SVGRepo_bgCarrier" stroke-width="0"></g>
                  <g id="SVGRepo_tracerCarrier" stroke-linecap="round" stroke-linejoin="round"></g>
                  <g id="SVGRepo_iconCarrier">
                    <path
                      d="M5 15L4 16V20H8L14 14M18 10L21 7L17 3L14 6M18 10L17 11M18 10L14 6M14 6L7.5 12.5"
                      stroke="#000000"
                      stroke-width="1.5"
                      stroke-linecap="round"
                      stroke-linejoin="round"
                    ></path>
                  </g>
                </svg>
                <p>Редактировать</p>
              </button>
              <div v-if="isEditing" class="edit-buttons">
                <button v-if="showButton" @click="saveChanges" class="save-button">
                  Сохранить
                </button>
                <button @click="cancelEditing" class="cancel-button">Отмена</button>
              </div>
            </div>
          </div>
        </div>
        <div class="arrows">
          <svg
            viewBox="0 0 24 24"
            fill="none"
            xmlns="http://www.w3.org/2000/svg"
            transform="rotate(180)"
            @click="loadPrevPost"
          >
            <g id="SVGRepo_bgCarrier" stroke-width="0"></g>
            <g id="SVGRepo_tracerCarrier" stroke-linecap="round" stroke-linejoin="round"></g>
            <g id="SVGRepo_iconCarrier">
              <path
                d="M7 12L17 12L13 16M15 10L13 8"
                stroke="#000000"
                stroke-width="1.5"
                stroke-linecap="round"
                stroke-linejoin="round"
              ></path>
            </g>
          </svg>
          <svg
            viewBox="0 0 24 24"
            @click="loadNextPost"
            fill="none"
            xmlns="http://www.w3.org/2000/svg"
          >
            <g id="SVGRepo_bgCarrier" stroke-width="0"></g>
            <g id="SVGRepo_tracerCarrier" stroke-linecap="round" stroke-linejoin="round"></g>
            <g id="SVGRepo_iconCarrier">
              <path
                d="M7 12L17 12L13 16M15 10L13 8"
                stroke="#000000"
                stroke-width="1.5"
                stroke-linecap="round"
                stroke-linejoin="round"
              ></path>
            </g>
          </svg>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.arrows {
  display: flex;
  justify-content: space-between;
}
.arrows > svg {
  width: 70px;
  height: 70px;
  cursor: pointer;
}
.cancel-button,
.save-button {
  border: solid 1px black;
  display: inline;
  border-radius: 5px;
  margin-left: 20px;
}
button {
  display: flex;
  background: transparent;
  border-style: none;
  font-weight: 500;
}
svg {
  width: 30px;
  height: 30px;
}
p {
  margin: 0;
}
.post-in {
  margin: auto;
  width: 1020px;
  height: 620px;
}
.two-blocks {
  display: flex;
  justify-content: space-between;
}
.left-block {
  display: flex;
  flex-direction: column;
  margin-top: 50px;
  align-items: center;
  height: 300px;
}
.right-block {
  margin-top: 10px;
}
.edit-buttons {
  margin-left: 20px;
}
.tags {
  position: relative;
  right: -60px;
  display: flex;
  margin-top: 20px;
  align-items: flex-end;
  font-weight: 500;
  font-size: 18px;
  border-bottom: solid 2px black;
  border-radius: 2px;
}
.tags > svg {
  width: 30px;
  height: 30px;
}
.post-header {
  display: flex;
  align-items: center;
  font-size: 28px;
  padding: 10px;
  justify-content: space-between;
  padding-left: 50px;
  font-weight: 500;
  margin-top: 10px;
}
.post-header > svg {
  width: 30px;
  height: 30px;
  cursor: pointer;
}
.title-input {
  font-size: 28px;
  font-weight: 500;
  padding: 0;
  width: 600px;
  border-style: none;
}
.author-job {
  padding-left: 20px;
}
.author-info {
  font-size: 18px;
  font-weight: 500;
  border: solid black 1px;
  width: 340px;
  border-radius: 15px;
  padding: 5px;
  margin-bottom: 10px;
  margin-left: 15px;
}
.author-name {
  display: flex;
  column-gap: 6px;
  align-items: flex-start;
  font-size: 20px;
}
.author-name > svg {
  width: 30px;
  height: 30px;
}
.post-back {
  width: 100vw;
  height: 100vh;
  background: rgba(0, 0, 0, 0.384);
  position: fixed;
  top: 0;
  left: 0;
}

.post {
  background: rgb(255, 255, 255);
  position: fixed;
  width: 1100px;
  height: 500px;

  top: 15vh;
  left: 40vh;
  border-radius: 8px;
}
.post-content,
.edit-body-textarea {
  width: 600px;
  font-size: 19px;
  text-align: justify;
  margin-top: 5px;
  border: solid 1px black;
  border-radius: 5px;
  padding: 10px;
}
.post-content-title,
.title-input {
  font-size: 22px;
  font-weight: 500;
}
.comment-author {
  font-weight: 500;
  font-size: 19px;
}
.com-inc {
  display: flex;
  align-items: c;
}
.com-inc > svg {
  width: 22px;
}
.comments {
  position: relative;
  display: block;
  width: 340px;
  max-width: 550px;
  margin-left: 20px;

  transition: 0.3s;
  overflow: hidden;
  border-radius: 15px;
  background: #c9d5dd;
}

.comments::after {
  position: absolute;
  right: 50px;
  top: 25px;
  content: '';
  display: block;

  width: 10px;
  height: 10px;
  border-right: 3px solid #000000;
  border-bottom: 3px solid #000000;
  transform: rotate(-45deg);
  transform-origin: center;
  transition: 0.3s;
}

.comments[open]::after {
  transform: rotate(45deg);
}
.comment-body {
  padding-left: 20px;
}
.comments[open] {
  padding-bottom: 20px;
}

.comments[open] .commets__title {
  margin-bottom: 12px;
}

.comments-title {
  background: #dee6ec;
  padding: 12px 24px;
  font-size: 20px;
  cursor: pointer;
  list-style: none;
  font-weight: 700;
  transition: 0.3s;
}

.comments-title::-webkit-details-marker {
  display: none;
}

.comments-content {
  padding: 10 22px;
  font-size: 18px;

  width: 300px;
  margin-left: 20px;
}

.comments-content p:last-child {
  margin-bottom: 0;
}
.comments-content p:first-child {
  margin-top: 0;
}
@media (max-width: 1800px) {
  .post {
    top: 60px;
    left: 205px;
  }
}
</style>
