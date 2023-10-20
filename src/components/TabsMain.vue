<template>
  <div class="main">
    <div class="tabs">
      <div :class="['one-tab active', ]">Посты</div>
      <div :class="['one-tab', {active}]">Пользователи</div>
    </div>
    <div class="tab-content">
      <div v-for="post in posts" :key="post.postId">
        <OnePost :user="post.title" :comment="post.body" :id="id"/>
      </div>
    </div>
  </div>
</template>

<script>
import OnePost from './OnePost.vue'
export default {
  name: 'TabsMain',
  components: {
    OnePost
  },
  data(){
    return {
      posts: [],
      comments: [],
      users: [],
      user: '',
      comment: ''
    }
  },
  
  methods: {
    /** Получить список постов */
    async getPosts(){
      const res = await fetch('https://jsonplaceholder.typicode.com/posts')
      const posts = await res.json()
      if(res.ok){
        this.posts = posts
      } else {
        console.log('Ошибка получения данных с сервера')
        throw Error
      }
    },
  },
  mounted(){
    this.getPosts()
  }
}
</script>

<style scoped>
.tabs {
  display: flex;
  gap: 30px;
  justify-content: center;
}
.active{
  background-color: grey;
}

.tab-content {
  margin: 0 auto;
  width: 83vw;
  border: 1px solid black;
  margin-top: 2rem;
}
.one-tab {
  width: 40vw;
  border-color: black;
  border-top: 1px solid;
  border-left: 1px solid;
  border-right: 1px solid;
}

</style>
