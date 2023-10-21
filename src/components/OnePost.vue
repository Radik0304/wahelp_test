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
  
<style lang="scss" scoped>
$border_one_post: 1px solid black;
$display_one_post_container: flex;
$add_comment_display: flex;
$direction_posts_list: column;
$direction_add_comment: column;
$width_add-comment_block: 50%;
$margin_align_center: 0 auto;
$header_color: grey;
$button_margin-top: 1rem;
$comment_textarea_margin: 1rem 0;
$comment_height: 100px;
$comment_border: 1px solid red;
$comment_header_weigth: bold;
$post_header_weigth: bold;

.one-post__container {
  border: $border_one_post;
  display: $display_one_post_container;
  flex-direction: $direction_posts_list;
}
.add-comment {
  display: $add_comment_display;
  flex-direction: $direction_add_comment;
  width: $width_add-comment_block;
  margin: $margin_align_center;
}
h3 {
  color: $header_color;
}

button {
  margin-top: $button_margin-top;
}
textarea {
  margin: $comment_textarea_margin;
  height: $comment_height;
}
.comments {
  border: $comment_border;
}
.comments {
  p {
    font-weight: $comment_header_weigth;
  }
}
.post {
  font-weight: $post_header_weigth;
}
</style>
  