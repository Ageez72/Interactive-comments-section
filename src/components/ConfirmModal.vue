<template>
  <div class="fixed inset-0 bg-black/50 flex items-center justify-center z-50" @click.self="$emit('cancel')">
    <div class="bg-white max-w-sm w-full rounded-md p-6 shadow-lg mx-3">
      <h2 class="text-lg font-bold text-gray-800 mb-2">Delete comment</h2>
      <p class="text-sm text-gray-600">
        Are you sure you want to delete this comment? This will remove the comment and can't be undone.
      </p>
      <div class="mt-5 flex justify-end gap-2">
        <button
          @click="$emit('cancel')"
          class="flex-1 bg-gray-200 text-gray-700 px-4 py-2 rounded hover:bg-gray-300 text-sm uppercase font-semibold"
        >
          No, Cancel
        </button>
        <button
          @click="$emit('confirm')"
          class="flex-1 bg-red-500 text-white px-4 py-2 rounded hover:bg-red-600 text-sm uppercase font-semibold"
        >
          Yes, Delete
        </button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  emits: ['confirm', 'cancel'],
};
</script>
