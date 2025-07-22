<template>
  <div>
    <div class="flex flex-col-reverse md:flex-row md:items-start gap-4 bg-white p-4 rounded-md shadow-sm mb-4 relative">
      <div
        class="flex md:flex-col items-center gap-2 bg-gray-100 px-4 md:px-2 py-1 md:py-3 text-center rounded-md w-fit text-sm text-gray-600">
        <button @click="vote(1)" class="hover:text-blue-500 font-bold text-center p-1">
          <svg xmlns="http://www.w3.org/2000/svg" width="11" height="11">
            <path
              d="M6.33 10.896c.137 0 .255-.05.354-.149.1-.1.149-.217.149-.354V7.004h3.315c.136 0 .254-.05.354-.149.099-.1.148-.217.148-.354V5.272a.483.483 0 0 0-.148-.354.483.483 0 0 0-.354-.149H6.833V1.4a.483.483 0 0 0-.149-.354.483.483 0 0 0-.354-.149H4.915a.483.483 0 0 0-.354.149c-.1.1-.149.217-.149.354v3.37H1.08a.483.483 0 0 0-.354.15c-.1.099-.149.217-.149.353v1.23c0 .136.05.254.149.353.1.1.217.149.354.149h3.333v3.39c0 .136.05.254.15.353.098.1.216.149.353.149H6.33Z"
              fill="#C5C6EF" />
          </svg>
        </button>
        <span class="font-bold py-2">{{ localComment.score }}</span>
        <button @click="vote(-1)" class="hover:text-blue-500 font-bold text-center p-1">
          <svg xmlns="http://www.w3.org/2000/svg" width="11" height="3">
            <path
              d="M9.256 2.66c.204 0 .38-.056.53-.167.148-.11.222-.243.222-.396V.722c0-.152-.074-.284-.223-.395a.859.859 0 0 0-.53-.167H.76a.859.859 0 0 0-.53.167C.083.437.009.57.009.722v1.375c0 .153.074.285.223.396a.859.859 0 0 0 .53.167h8.495Z"
              fill="#C5C6EF" />
          </svg>
        </button>
      </div>

      <div class="flex-1 space-y-2">
        <div class="flex justify-between items-center flex-wrap gap-2">
          <div class="flex items-center gap-2">
            <img :src="comment.user.image.png" class="w-6 h-6 rounded-full" />
            <span class="font-bold text-gray-800">{{ comment.user.username }}</span>
            <span v-if="isCurrentUser" class="bg-blue-500 text-white px-1 text-xs rounded you-tag">you</span>
            <span class="text-sm text-gray-500">{{ comment.createdAt }}</span>
          </div>
          <div class="flex items-center gap-4 text-sm font-semibold absolute bottom-5 right-5 md:static">
            <button v-if="isCurrentUser" @click="showDelete = true"
              class="font-bold text-red-500 hover:underline flex items-center gap-1">
              <svg xmlns="http://www.w3.org/2000/svg" width="12" height="14">
                <path
                  d="M1.167 12.448c0 .854.7 1.552 1.555 1.552h6.222c.856 0 1.556-.698 1.556-1.552V3.5H1.167v8.948Zm10.5-11.281H8.75L7.773 0h-3.88l-.976 1.167H0v1.166h11.667V1.167Z"
                  fill="#ED6368" />
              </svg>
              Delete</button>
            <button v-if="isCurrentUser" @click="isEditing = !isEditing"
              class="font-bold text-blue-500 hover:underline flex items-center gap-1">
              <svg xmlns="http://www.w3.org/2000/svg" width="14" height="14">
                <path
                  d="M13.479 2.872 11.08.474a1.75 1.75 0 0 0-2.327-.06L.879 8.287a1.75 1.75 0 0 0-.5 1.06l-.375 3.648a.875.875 0 0 0 .875.954h.078l3.65-.333c.399-.04.773-.216 1.058-.499l7.875-7.875a1.68 1.68 0 0 0-.061-2.371Zm-2.975 2.923L8.159 3.449 9.865 1.7l2.389 2.39-1.75 1.706Z"
                  fill="#5357B6" />
              </svg>
              Edit</button>
            <button v-else @click="isReplying = !isReplying"
              class="font-bold text-blue-500 hover:underline flex items-center gap-1">
              <svg xmlns="http://www.w3.org/2000/svg" width="14" height="13">
                <path
                  d="M.227 4.316 5.04.16a.657.657 0 0 1 1.085.497v2.189c4.392.05 7.875.93 7.875 5.093 0 1.68-1.082 3.344-2.279 4.214-.373.272-.905-.07-.767-.51 1.24-3.964-.588-5.017-4.829-5.078v2.404c0 .566-.664.86-1.085.496L.227 5.31a.657.657 0 0 1 0-.993Z"
                  fill="#5357B6" />
              </svg>
              Reply</button>
          </div>
        </div>

        <div v-if="!isEditing" class="text-gray-700">
          <span v-if="comment.replyingTo" class="text-blue-500 font-semibold">@{{ comment.replyingTo }}</span>
          {{ comment.content }}
        </div>
        <div class="flex flex-col" v-else>
          <textarea v-model="editContent" class="w-full p-2 border rounded resize-none" rows="5"></textarea>
          <button @click="submitEdit"
            class="ml-auto font-bold mt-2 bg-blue-500 text-white px-3 py-1 rounded uppercase font-semibold">Update</button>
        </div>
      </div>
    </div>

    <CommentForm v-if="isReplying" :current-user="currentUser" :reply-to="comment.user.username" @submit="submitReply"
      @cancel="isReplying = false" />

    <div v-if="comment.replies?.length" class="border-l-2 border-gray-200 ml-0 lg:ml-6 pl-4 mt-4 space-y-4">
      <CommentItem v-for="reply in comment.replies" :key="reply.id" :comment="reply" :comments="comments"
        :parent-replies="comment.replies" :current-user="currentUser" @update-comments="updateComments" />
    </div>

    <ConfirmModal v-if="showDelete" @confirm="deleteComment" @cancel="showDelete = false" />
  </div>
</template>

<script>
import CommentForm from './CommentForm.vue';
import ConfirmModal from './ConfirmModal.vue';

export default {
  components: { CommentForm, ConfirmModal },
  props: {
    comment: Object,
    comments: Array,
    parentReplies: Array,
    currentUser: Object,
  },
  data() {
    return {
      isReplying: false,
      isEditing: false,
      showDelete: false,
      editContent: this.comment.content,
      localComment: { ...this.comment },
    };
  },
  computed: {
    isCurrentUser() {
      return this.comment.user.username === this.currentUser.username;
    },
  },
  methods: {
    vote(amount) {
      const updateScoreInTree = (items) => {
        return items.map((item) => {
          if (item.id === this.comment.id) {
            return { ...item, score: item.score + amount };
          }
          if (item.replies) {
            item.replies = updateScoreInTree(item.replies);
          }
          return item;
        });
      };

      this.localComment.score += amount;

      const updated = updateScoreInTree(this.comments);
      this.$emit('update-comments', updated);
    },
    submitReply(newContent) {
      const newReply = {
        id: Date.now(),
        content: newContent.content,
        createdAt: 'Just now',
        score: 0,
        replyingTo: newContent.replyTo,
        user: this.currentUser,
      };

      const updatedComments = this.comments.map((c) => {
        if (c.id === this.comment.id) {
          return { ...c, replies: [...(c.replies || []), newReply] };
        }
        return c;
      });

      this.$emit('update-comments', updatedComments);
      this.isReplying = false;
    },
    submitEdit() {
      const editTree = (items) => {
        return items.map((item) => {
          if (item.id === this.comment.id) {
            return { ...item, content: this.editContent };
          }
          if (item.replies) {
            item.replies = editTree(item.replies);
          }
          return item;
        });
      };

      const updated = editTree(this.comments);
      this.$emit('update-comments', updated);
      this.isEditing = false;
    },
    deleteComment() {
      const deleteFromTree = (items, targetId) => {
        return items
          .map((item) => {
            if (item.id === targetId) return null;
            if (item.replies) {
              item.replies = deleteFromTree(item.replies, targetId);
            }
            return item;
          })
          .filter(Boolean);
      };

      const updated = deleteFromTree(this.comments, this.comment.id);
      this.$emit('update-comments', updated);
      this.showDelete = false;
    },
  },
};
</script>
