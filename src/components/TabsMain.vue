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
        class="filtered-user_header"
        v-if="is_active_one_user && is_active_posts"
      >
        <h2>Посты пользователя {{ name_filtered_user }}</h2>
        <button class="close" @click="clearFilter"></button>
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
          @getPosts="getPosts"
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
          @showPostsUser="showPostsUser"
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
      /** Массивы */
      posts: [], //посты
      comments: [], //комменты
      users: [], //пользователи

      /** Cписки */
      users_keyBy: {}, // список пользователей по id
      comments_groupBy: {}, // список комментарий для каждого поста

      /** Статусы */
      is_active_posts: true, //активен таб с постами
      is_active_users: false, //активен таб с пользователями
      is_data_await: true, // загрузка данных
      name_filtered_user: "", // имя пользователя, по котормоу фильтруем
      is_active_one_user: false, // смотрим посты одного пользователя
      page: 1 /* текущая страница */,
      limit: 20 /* количество постов получаемых за один раз */,
    };
  },

  methods: {
    /** Получить список постов */
    async getPosts() {
      const res = await fetch(
        `https://jsonplaceholder.typicode.com/posts?_page=${this.page}&_limit=${this.limit}`
      );
      const posts = await res.json();
      this.is_data_await = true;

      if (res.ok) {
        this.is_data_await = false;
        this.posts.push(...posts);
      } else {
        console.log("Ошибка получения данных с сервера");
        throw Error;
      }
    },

    /** Получить отфильтрованный список постов */
    async getFilteredPosts(userId) {
      this.posts = [];
      const res = await fetch(
        `https://jsonplaceholder.typicode.com/posts?userId=${userId}`
      );
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
      this.users = [];
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
      this.comments = [];
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
      this.name_filtered_user = username;
      this.posts = [];
      this.getFilteredPosts(userId);
    },

    /** Сбросить фильтр */
    clearFilter() {
      this.posts = [];
      this.is_active_one_user = false;
      this.getPosts();
    },

    /** Скроллинг */
    fHandleScroll() {
      if (
        window.scrollY + window.innerHeight  >= document.body.scrollHeight - 50 &&
        !this.is_active_one_user &&
        !this.is_data_await
      ) {
        this.page++;
          this.getPosts();
      }
    },
  },
  mounted() {
    this.getPosts();
    this.getComments();
    this.getUsers();
    window.addEventListener("scroll", this.fHandleScroll);
  },

  beforeUnmount() {
    window.removeEventListener("scroll", this.fHandleScroll);
  },
};
</script>

<style lang="scss" scoped>
$border_black: 1px solid black;
$border_width: 1px solid;
$margin_align_center: 0 auto;
$tab_content_width: 83vw;
$tab_content_margin_top: 2rem;
$filtered_user_header_display: flex;
$content_align_header: center;
$button_close_size: 20px;
$margin_left_close_button: 1rem;
$background_close_button: url("../assets/close.svg");
$hidden_view_block: none;
$cursor: pointer;
$tabs_display: flex;
$tabs_gap: 30px;
$tabs_content_align: center;
$active_tab_color: grey;

.tabs {
  display: $tabs_display;
  gap: $tabs_gap;
  justify-content: $tabs_content_align;
}
.active {
  background-color: $active_tab_color;
}

.tab-content {
  margin: $margin_align_center;
  width: $tab_content_width;
  border: $border_black;
  margin-top: $tab_content_margin_top;
}
.posts-tab,
.users-tab {
  width: 40vw;
  border-color: black;
  border-top: $border_width;
  border-left: $border_width;
  border-right: $border_width;
}

.posts-tab:hover,
.users-tab:hover {
  cursor: $cursor;
}

.hidden {
  display: $hidden_view_block;
}
.filtered-user_header {
  display: $filtered_user_header_display;
  justify-content: $content_align_header;
  align-items: $content_align_header;
}
.filtered-user_header {
  button {
    width: $button_close_size;
    height: $button_close_size;
    margin-left: $margin_left_close_button;
    background-image: $background_close_button;

    &:hover {
      cursor: $cursor;
    }
  }
}
</style>
