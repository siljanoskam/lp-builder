<template>
  <div
      v-if="isHovered"
      class="flex justify-center space-x-2 mt-2"
  >
    <button @click="duplicateCallback" class="bg-white text-gray-700 hover:bg-gray-100 rounded p-2">Duplicate</button>
    <button @click="deleteCallback" class="bg-gray-700 text-white hover:bg-gray-600 rounded p-2">Delete</button>
  </div>
</template>

<script>
export default {
  props: {
    isHovered: {
      type: Boolean,
      required: true
    },

    duplicateCallback: {
      type: Function,
      required: true
    },

    deleteCallback: {
      type: Function,
      required: true
    }
  },
}
</script>