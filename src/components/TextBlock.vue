<template>
  <div
      :draggable="true"
      class="relative border border-dashed bg-white p-4"
      @dragstart="onDragStart"
      @mouseenter="isHovered = true"
      @mouseleave="isHovered = false"
  >
    <textarea
        ref="textBlock"
        v-model="textBlockContent"
        class="w-full p-2 rounded-md"
        placeholder="Enter text..."
        @input="updateContent"
    ></textarea>

    <div
        v-if="isHovered"
        class="flex justify-center space-x-2 mt-2"
    >
      <button @click="duplicateBlock" class="bg-white text-gray-700 rounded p-1">Duplicate</button>
      <button @click="deleteBlock" class="bg-gray-700 text-white rounded p-1">Delete</button>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    id: {
      type: Number,
      required: true,
    },

    content: {
      type: String,
      default: '',
    },
  },

  data() {
    return {
      textBlockContent: this.content,
      isHovered: false,
    };
  },

  watch: {
    content(newVal) {
      this.textBlockContent = newVal;
    },
  },

  mounted() {
    this.adjustTextBlockSize();
  },

  methods: {
    onDragStart(event) {
      event.dataTransfer.setData('text/plain', `${this.id}`);
      event.dataTransfer.effectAllowed = 'move';
    },

    updateContent() {
      this.adjustTextBlockSize();
      this.$emit('update:content', this.textBlockContent, this.id); // Emit updated content
    },

    adjustTextBlockSize() {
      const textarea = this.$refs.textBlock;

      if (textarea) {
        textarea.style.height = 'auto';
        textarea.style.height = `${textarea.scrollHeight}px`;
      }
    },

    duplicateBlock() {
      this.$emit('duplicate', this.id);
    },

    deleteBlock() {
      this.$emit('delete', this.id);
    },
  },
};
</script>

<style scoped>
  textarea {
    resize: none;
  }

  textarea:disabled {
    background: white;
  }
</style>
