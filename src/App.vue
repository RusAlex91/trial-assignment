<template>
  <div class="posts">
    <Post
      v-for="post in posts"
      v-bind:key="post.date"
      :postDate="post.date"
      :authorName="post.authorName"
      :authorUrl="post.authorUrl"
      :content="post.content"
      :contentPostTones="post.contentPostTones"
    />
    <div class="loader">
      <div class="circle"></div>
      <div class="circle"></div>
      <div class="circle"></div>
    </div>
  </div>
</template>

<script>
import Post from './components/Post.vue'
import * as dataFeed from './assets/data/feed.json'
export default {
  data () {
    return {
      data: dataFeed,
      posts: null,
      postPage: 1
    }
  },
  name: 'App',
  components: {
    Post
  },
  methods: {
    getData (limit = 9) {
      let allPosts = this.data.default
      let maxLimit = limit * this.postPage
      let tempPosts = []
      for (let post = 0; post < allPosts.length; post++) {
        tempPosts.push(allPosts[post])
        if (post == maxLimit) {
          this.posts = tempPosts
          break
        }
      }
      console.log(this.posts)
    }
  },
  mounted: function () {
    this.getData()
    window.addEventListener('scroll', () => {
      const { scrollTop, scrollHeight, clientHeight } = document.documentElement
      if (scrollTop + clientHeight >= scrollHeight - 5) {
        const loading = document.getElementsByClassName('loader')[0]
        loading.classList.add('show')

        setTimeout(() => {
          loading.classList.remove('show')
          this.postPage++
          this.getData(10)
        }, 3000)
      }
    })
  }
}
</script>

<style>
.posts {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

.loader {
  opacity: 0;
  display: flex;
  justify-content: center;
  align-items: center;
  position: fixed;
  bottom: 50px;
  transition: opacity 0.3s ease-in;
  width: 100%;
  height: 5vh;
  background-color: rgba(255, 9, 173, 0.5);
}

.loader.show {
  opacity: 1;
}

.circle {
  background-color: #000;
  width: 10px;
  height: 10px;
  border-radius: 50%;
  margin: 5px;
  animation: bounce 0.5s ease-in infinite;
}

.circle:nth-of-type(2) {
  animation-delay: 0.1s;
}

.circle:nth-of-type(3) {
  animation-delay: 0.2s;
}

@keyframes bounce {
  0%,
  100% {
    transform: translateY(0);
  }
  50% {
    transform: translateY(-10px);
  }
}
</style>
