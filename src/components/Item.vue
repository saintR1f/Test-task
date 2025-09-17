<script setup>
import { inject, ref } from 'vue'

const props = defineProps({
  postTitle: String,
  postBody: String,
  postTags: String,
  postLikes: Number,
  postDislikes: Number,
  postViews: Number,
  postUserId: Number,
  postId: Number,
})

// userId.value = props.postUserId
const { openFullPost } = inject('Actions')
</script>

<template>
  <div class="post">
    <div class="post-znak">
      <svg
        viewBox="0 0 24 24"
        fill="none"
        xmlns="http://www.w3.org/2000/svg"
        @click="openFullPost(postId)"
        style="cursor: pointer"
      >
        <g id="SVGRepo_bgCarrier" stroke-width="0"></g>
        <g id="SVGRepo_tracerCarrier" stroke-linecap="round" stroke-linejoin="round"></g>
        <g id="SVGRepo_iconCarrier">
          <path
            opacity="0.5"
            d="M2 12C2 7.28595 2 4.92893 3.46447 3.46447C4.92893 2 7.28595 2 12 2C16.714 2 19.0711 2 20.5355 3.46447C22 4.92893 22 7.28595 22 12"
            stroke="#1C274C"
            stroke-width="1.5"
          ></path>
          <path
            d="M2 14C2 11.1997 2 9.79961 2.54497 8.73005C3.02433 7.78924 3.78924 7.02433 4.73005 6.54497C5.79961 6 7.19974 6 10 6H14C16.8003 6 18.2004 6 19.27 6.54497C20.2108 7.02433 20.9757 7.78924 21.455 8.73005C22 9.79961 22 11.1997 22 14C22 16.8003 22 18.2004 21.455 19.27C20.9757 20.2108 20.2108 20.9757 19.27 21.455C18.2004 22 16.8003 22 14 22H10C7.19974 22 5.79961 22 4.73005 21.455C3.78924 20.9757 3.02433 20.2108 2.54497 19.27C2 18.2004 2 16.8003 2 14Z"
            stroke="#1C274C"
            stroke-width="1.5"
          ></path>
          <path
            d="M12 17L12 11M12 11L14.5 13.5M12 11L9.5 13.5"
            stroke="#1C274C"
            stroke-width="1.5"
            stroke-linecap="round"
            stroke-linejoin="round"
          ></path>
        </g>
      </svg>
    </div>
    <div class="item-info">
      <p @click="openFullPost(postId)" class="title-link">{{ postTitle }}</p>

      <p class="post-body">{{ postBody }}</p>
      <div class="post-reactions">
        <div class="post-likes">
          <svg viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
            <g id="SVGRepo_bgCarrier" stroke-width="0"></g>
            <g id="SVGRepo_tracerCarrier" stroke-linecap="round" stroke-linejoin="round"></g>
            <g id="SVGRepo_iconCarrier">
              <path
                d="M8 14V20M8 20L4 20V9.99998H8L13.1956 3.93847C13.6886 3.3633 14.4642 3.11604 15.1992 3.29977L15.2467 3.31166C16.5885 3.64711 17.1929 5.21057 16.4258 6.36135L14 9.99998H18.5604C19.8225 9.99998 20.7691 11.1546 20.5216 12.3922L19.3216 18.3922C19.1346 19.3271 18.3138 20 17.3604 20M8 20H12"
                stroke="#000000"
                stroke-width="1.5"
                stroke-linecap="round"
                stroke-linejoin="round"
              ></path>
            </g>
          </svg>
          <p>{{ postLikes }}</p>
        </div>
        <div class="dis-likes">
          <svg viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
            <g id="SVGRepo_bgCarrier" stroke-width="0"></g>
            <g id="SVGRepo_tracerCarrier" stroke-linecap="round" stroke-linejoin="round"></g>
            <g id="SVGRepo_iconCarrier">
              <path
                d="M8 10V4M8 4L4 4.00002V14H8L13.1956 20.0615C13.6886 20.6367 14.4642 20.884 15.1992 20.7002L15.2467 20.6883C16.5885 20.3529 17.1929 18.7894 16.4258 17.6387L14 14H18.5604C19.8225 14 20.7691 12.8454 20.5216 11.6078L19.3216 5.60779C19.1346 4.67294 18.3138 4.00002 17.3604 4.00002M8 4H12"
                stroke="#000000"
                stroke-width="1.5"
                stroke-linecap="round"
                stroke-linejoin="round"
              ></path>
            </g>
          </svg>
          <p>{{ postDislikes }}</p>
        </div>
        <div class="post-views">
          <svg viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
            <g id="SVGRepo_bgCarrier" stroke-width="0"></g>
            <g id="SVGRepo_tracerCarrier" stroke-linecap="round" stroke-linejoin="round"></g>
            <g id="SVGRepo_iconCarrier">
              <path
                d="M22 12C22 12 18.3636 19 12 19C5.63636 19 2 12 2 12C2 12 5.63636 5 12 5C14.8779 5 17.198 6.43162 18.8762 8M9 12C9 13.6569 10.3431 15 12 15C13.6569 15 15 13.6569 15 12C15 10.3431 13.6569 9 12 9"
                stroke="#000000"
                stroke-width="1.5"
                stroke-linecap="round"
                stroke-linejoin="round"
              ></path>
            </g>
          </svg>
          <p>{{ postViews }}</p>
        </div>
      </div>
      <div class="post-tags">
        <p>{{ 'UserID: ' + postUserId }}</p>
      </div>
    </div>
  </div>
</template>

<style scoped>
.post {
  width: 420px;
  height: auto;
  background: rgba(18, 30, 46, 0.747);
  display: flex;
  column-gap: 10px;
  border: solid 1px rgb(45, 59, 78);
  margin: 20px;
  padding: 20px;
  border-radius: 8px 8px 8px 8px;
}

.post-reactions,
.post-likes,
.dis-likes,
.post-views,
.post-tags {
  display: flex;
  padding: 1px;
  color: rgb(255, 255, 255);
}
.post-tags {
  color: rgb(156, 156, 156);
}

.dis-likes {
  margin: 0px 5px 0px 5px;
}
.item-title {
  display: flex;
  align-items: center;
}
p {
  font-family:
    'Trebuchet MS', 'Lucida Sans Unicode', 'Lucida Grande', 'Lucida Sans', Arial, sans-serif;

  margin: 0;
}
.title-link {
  color: rgb(11, 96, 199);
  border-bottom: solid 1px rgb(11, 96, 199);
  cursor: pointer;
  display: inline;
}
.title-link:hover {
  border-bottom: solid 1px rgb(0, 115, 255);
  color: rgb(3, 98, 214);
}
svg {
  fill: white;
  width: 22px;
  display: inline-block;
}
.post > svg {
  width: 30px;
  height: 30px;
}
.post-body {
  text-align: justify;
  color: white;
}
.post-tags > svg {
  fill: none;
}
</style>
