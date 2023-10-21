<template>
    <div class="user_container">
        <h3>{{ $props.user }}</h3>
        <button>просмотреть комментарии</button>
    </div>
  </template>
  
  <script>
  export default {
    name: "OneUser",
    props: {
        user: String,
        userId: Number
    }
  };
  </script>
  
  <style scoped>
  </style>