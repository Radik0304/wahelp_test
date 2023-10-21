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
      <div class="filtered-user_header" v-if="is_active_one_user && is_active_posts">
        <h2>Посты пользователя {{ name_filtered_user }}</h2>
        <button @click="clearFilter">Сбросить фильтр</button>
      </div>
      <div
        v-for="post in posts"
        :key="post.id"
        :class="['posts_list', { hidden: !is_active_posts }]"
      >
        <OnePost
          :user="users_keyBy[post.userId]?.name"
          :post="post.body"
          :comments="comments_groupBy[post.id]"
          :postId="post.id"
        />
      </div>
      <p v-if="is_data_await">Данные загружаются...</p>
      <div
        v-for="user in users"
        :key="user.id"
        :class="['users_list', { hidden: !is_active_users }]"
      >
        <OneUser
          :userId="user.id"
          :username="user.name"
          :is_active_posts="is_active_posts"
          :is_active_users="is_active_users"
          v-on:showPostsUser="showPostsUser"
        />
      </div>
      
    </div>
  </div>
</template>

<script>
import OnePost from "./OnePost.vue";
import OneUser from "./OneUser.vue";
// import OneUserPosts from './OneUserPosts.vue';
import _ from "lodash";
export default {
  name: "TabsMain",
  components: {
    OnePost,
    OneUser,
    // OneUserPosts,
  },
  data() {
    return {
      posts: [], //посты
      comments: [], //комменты
      users: [], //пользователи
      is_active_posts: true, //активен таб с постами
      is_active_users: false, //активен таб с пользователями
      is_data_await: true, // загрузка данных
      name_filtered_user: '', // имя пользователя, по котормоу фильтруем
      is_active_one_user: false, // смотрим посты одного пользователя
      users_keyBy: {}, // список пользователей по id
      comments_groupBy: {}, // список комментарий для каждого поста
    };
  },

  methods: {
    /** Получить список постов */
    async getPosts() {
      const res = await fetch(`https://jsonplaceholder.typicode.com/posts`);
      const posts = await res.json();
      if (res.ok) {
        this.posts = posts;
      } else {
        console.log("Ошибка получения данных с сервера");
        throw Error;
      }
    },

    /** Получить отфильтрованный список постов */
    async getFilteredPosts(userId) {
      const res = await fetch(`https://jsonplaceholder.typicode.com/posts?userId=${userId}`);
      const posts = await res.json();
      this.is_data_await = true;
      if (res.ok) {
        this.is_data_await = false;
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
      this.is_data_await = true;
      if (res.ok) {
        this.is_data_await = false;
        this.users = users;
        this.users_keyBy = _.keyBy(users, "id");
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
        this.comments_groupBy = _.groupBy(comments, "postId");
      } else {
        console.log("Ошибка получения данных с сервера");
        throw Error;
      }
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

    /** Просмотреть посты пользователя */
    showPostsUser(userId, username) {
      this.selectPostsTab();
      this.is_active_one_user = true;
      this.name_filtered_user = username
      this.posts=[];
      this.getFilteredPosts(userId)
    },

    /** Сбросить фильтр */
    clearFilter(){
      this.posts=[];
      this.is_active_one_user = false;
      this.getPosts()
    }
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
.filtered-user_header{
  display: flex;
  justify-content: center;
  align-items: center;
}
.filtered-user_header button{
  width: 200px;
  height: 20px;
  margin-left: 1rem;
}
</style>
