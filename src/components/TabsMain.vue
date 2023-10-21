<template>
  <div class="main">
    <div class="tabs">
      <div
        :class="['posts-tab', { active: is_active_posts }]"
        @click="selectPostsTab"
      >
        Посты
      </div>
      <div
        :class="['users-tab', { active: is_active_users }]"
        @click="selectUsersTab"
      >
        Пользователи
      </div>
    </div>
    <div class="tab-content">
      <div
        v-for="post in posts"
        :key="post.postId"
        :class="['posts_list', { hidden: !is_active_posts }]"
      >
        <OnePost :user="users_keyBy[post.userId]?.name" :comment="post.body" />
      </div>
      <!-- {{users_keyBy}} -->
    </div>
  </div>
</template>

<script>
import OnePost from "./OnePost.vue";
import _ from 'lodash'
export default {
  name: "TabsMain",
  components: {
    OnePost,
  },
  data() {
    return {
      posts: [], //посты
      comments: [], //комменты
      users: [], //пользователи
      is_active_posts: true, //активен таб с постами
      is_active_users: false, //активен таб с пользователями
      posts_keyBy:{},
      users_keyBy:{},
      comments_keyBy:{}
    };
  },

  methods: {
    /** Получить список постов */
    async getPosts() {
      const res = await fetch("https://jsonplaceholder.typicode.com/posts");
      const posts = await res.json();
      if (res.ok) {
        this.posts = posts;
      } else {
        console.log("Ошибка получения данных с сервера");
        throw Error;
      }
    },

    /** Получить список пользователей */
    async getUsers() {
      const res = await fetch("https://jsonplaceholder.typicode.com/users");
      const users = await res.json();
      if (res.ok) {
        this.users = users;
        this.users_keyBy = _.keyBy(users, 'id')
        console.log(this.users)
        console.log(this.users_keyBy)
      } else {
        console.log("Ошибка получения данных с сервера");
        throw Error;
      }
    },

    /** Получить список комментариев */
    async getComments() {
      const res = await fetch("https://jsonplaceholder.typicode.com/comments");
      const comments = await res.json();
      if (res.ok) {
        this.comments = comments;
      } else {
        console.log("Ошибка получения данных с сервера");
        throw Error;
      }
    },

    get fuck() {
      return this.users_keyBy
    },
    /** Выбрать таб с постами */
    selectPostsTab() {
      this.is_active_posts = true;
      this.is_active_users = false;
    },

    /** Выбрать таб с пользователями */
    selectUsersTab() {
      this.is_active_posts = false;
      this.is_active_users = true;
    },
  },
created() {
    this.getPosts();
    this.getUsers();
    this.getComments();
  },
};
</script>

<style scoped>
.tabs {
  display: flex;
  gap: 30px;
  justify-content: center;
}
.active {
  background-color: grey;
}

.tab-content {
  margin: 0 auto;
  width: 83vw;
  border: 1px solid black;
  margin-top: 2rem;
}
.posts-tab,
.users-tab {
  width: 40vw;
  border-color: black;
  border-top: 1px solid;
  border-left: 1px solid;
  border-right: 1px solid;
}

.posts-tab:hover,
.users-tab:hover {
  cursor: pointer;
}

.hidden {
  display: none;
}
</style>
