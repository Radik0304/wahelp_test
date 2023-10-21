<template>
    <div class="one-user-posts_container">
      <h3></h3>
    </div>
  </template>
  
  <script>
  export default {
    name: "OneUserPosts",
    props: {
      username: String,
      posts: Array,
    }
  };
  </script>
  
  <style scoped>
  </style>