<template>
  <div class="max-w-5xl mx-auto p-4">
    <CommentItem
      v-for="comment in state.comments"
      :key="comment.id"
      :comment="comment"
      :current-user="state.currentUser"
      :comments="state.comments"
      @update-comments="updateComments"
    />

    <!-- NEW COMMENT FORM -->
    <CommentForm
      :current-user="state.currentUser"
      :show-cancel="false"
      @submit="submitNewComment"
    />
  </div>
</template>

<script>
import CommentItem from './components/CommentItem.vue';
import CommentForm from './components/CommentForm.vue';

export default {
  components: {
    CommentItem,
    CommentForm,
  },
  data() {
    return {
      state: {
        comments: [],
        currentUser: null,
      },
    };
  },
  mounted() {
    const saved = localStorage.getItem('commentsData');
    if (saved) {
      const parsed = JSON.parse(saved);
      this.state.comments = parsed.comments;
      this.state.currentUser = parsed.currentUser;
    } else {
      fetch('/data.json')
        .then((res) => res.json())
        .then((data) => {
          this.state.comments = data.comments;
          this.state.currentUser = data.currentUser;
          localStorage.setItem('commentsData', JSON.stringify(data));
        });
    }
  },
  watch: {
    state: {
      handler(newVal) {
        localStorage.setItem('commentsData', JSON.stringify(newVal));
      },
      deep: true,
    },
  },
  methods: {
    updateComments(newComments) {
      this.state.comments = newComments;
    },
    submitNewComment({ content }) {
      const newComment = {
        id: Date.now(),
        content,
        createdAt: 'Just now',
        score: 0,
        user: this.state.currentUser,
        replies: [],
      };
      this.state.comments.push(newComment);
    },
  },
};
</script>
