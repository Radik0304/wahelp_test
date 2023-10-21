<template>
    <div class="user_container">
        <h3>{{ $props.username }}</h3>
        <button @click="showPosts">просмотреть комментарии</button>
    </div>
  </template>
  
  <script>
  export default {
    name: "OneUser",
    props: {
        username: String,
        userId: Number,
    },
    methods: {
      showPosts(){
        this.$emit('showPostsUser', this.$props.userId, this.$props.username)
      }
    }
  };

  </script>
  
  <style scoped>
  </style>