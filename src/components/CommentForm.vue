<template>
  <div class="mt-4 bg-white p-4 rounded-md shadow-sm flex flex-col md:flex-row gap-4 items-start">
    <img :src="currentUser?.image.png" class="w-8 h-8 rounded-full" />
    <textarea
      v-model="content"
      class="flex-1 border border-gray-300 p-2 rounded-md w-full resize-none focus:outline-none focus:ring-1 focus:ring-blue-500"
      rows="3"
      placeholder="Add a comment..."
    ></textarea>
    <div class="flex gap-2 items-center mt-2 md:mt-0">
      <button v-if="showCancel" @click="onCancel" class="text-sm text-gray-500 hover:text-gray-700">Cancel</button>
      <button @click="onSubmit" class="bg-blue-500 hover:bg-blue-600 text-white px-4 py-2 rounded text-sm uppercase font-semibold">
        {{ replyTo ? 'Reply' : 'Send' }}
      </button>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    currentUser: Object,
    replyTo: String,
    showCancel: {
      type: Boolean,
      default: true,
    },
  },
  data() {
    return {
      content: this.replyTo ? `@${this.replyTo} ` : '',
    };
  },
  methods: {
    onSubmit() {
      if (!this.content.trim()) return;
      this.$emit('submit', {
        content: this.content,
        replyTo: this.replyTo,
      });
      this.content = '';
    },
    onCancel() {
      this.$emit('cancel');
    },
  },
};
</script>
