<template>
  <div class="one-post__container">
    <h3>{{ $props.idUser }}</h3>
    <span>{{ $props.comment }}</span>
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
    comment: String,
    idUser: Number,
  },
  data() {
    return {
      enteredComment: "", // введенный текст комментария
    };
  },
  methods: {
    /** Добавить комментарий */
    async addComment() {
        const pushedData = {
            id: this.$props.id,
            body: this.enteredComment,
        }
      const res = await fetch("https://jsonplaceholder.typicode.com/comments", {
        method: "POST",
        body: JSON.stringify(pushedData),
        headers: {
          "Content-Type": "application/json",
        },
      });
      if(res.ok){
        alert('комментарий добавлен')
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
</style>
  