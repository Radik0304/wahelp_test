<template>
  <div class="one-post__container">
    <h3>{{ $props.user }}</h3>
    <span class="post">{{ $props.post }}</span>
    <span class="comments">
      <p>comments:</p>
      <ul v-for="comment in $props.comments" :key="comment.id">
        <li>
          {{ comment.body }}
        </li>
      </ul>
    </span>
    <div class="add-comment">
      <button @click="addComment">Добавить комментарий</button>
      <textarea type="text" v-model="enteredComment"></textarea>
    </div>
  </div>
</template>
  
  <script>
export default {
  name: "OnePost",
  props: {
    user: String,
    post: String,
    comments: Array,
    idUser: Number,
    postId: Number,
  },
  data() {
    return {
      enteredComment: "", // введенный текст комментария
    };
  },
  methods: {
    /** Добавить комментарий */
    async addComment() {
      const newCommentData = {
        postId: this.$props.postId,
        body: this.enteredComment,
        email: 'any@mail.com'
      }
      const res = await fetch(`https://jsonplaceholder.typicode.com/comments?postId=${this.$props.postId}`, {
        method: "POST",
        body: JSON.stringify(newCommentData),
        headers: {
          "Content-Type": "application/json",
        },
      });
      if(res.ok){
        alert('комментарий добавлен')
        this.$emit('getPosts')
      } else {
        console.log('Ошибка отправки данных')
      }
    },
  },
};
</script>
  
<style scoped>
.one-post__container {
  border: 1px solid black;
  display: flex;
  flex-direction: column;
}
.add-comment {
  display: flex;
  flex-direction: column;
  width: 50%;
  margin: 0 auto;
}
h3 {
  color: grey;
}

button {
  margin-top: 1rem;
}
textarea {
  margin-top: 1rem;
  margin-bottom: 1rem;
  height: 100px;
}
.comments {
  border: 1px solid red;
}
.comments p {
  font-weight: bold;
}
.post {
  font-weight: bold;
}
</style>
  