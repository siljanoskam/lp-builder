<template>
  <div
      :draggable="true"
      class="relative p-4 border border-dashed rounded flex flex-col items-center space-y-2"
      @contextmenu.prevent="showContextMenu($event)"
      @dragstart="onDragStart"
      @mouseenter="isHovered = true"
      @mouseleave="isHovered = false"
  >
    <img
        v-if="image"
        :src="image"
        alt="Selected image"
        class="w-fit object-cover rounded"
    />

    <div
        v-if="contextMenuVisible"
        class="absolute bg-white border rounded shadow p-2 z-50"
        :style="{ top: contextMenuY + 'px', left: contextMenuX + 'px' }"
    >
      <h4 class="p-2 bg-gray-700 text-white">Replace image with:</h4>
      <p
          v-for="img in predefinedImages"
          :key="img.url"
          @click="selectImage(img.url)"
          class="cursor-pointer hover:bg-gray-100 p-2 rounded"
      >
        {{ img.label }}
      </p>
    </div>

    <BlockOperations
        :is-hovered="isHovered"
        :duplicate-callback="duplicateBlock"
        :delete-callback="deleteBlock"
    />
  </div>
</template>

<script>
import BlockOperations from "./BlockOperations";

export default {
  components: {
    BlockOperations
  },

  props: {
    id: {
      type: Number,
      required: true,
    },

    image: {
      type: String,
      required: true,
    },

    predefinedImages: {
      type: Array,
      required: true,
    },
  },

  data() {
    return {
      contextMenuVisible: false,
      contextMenuX: 0,
      contextMenuY: 0,
      isHovered: false,
    };
  },

  methods: {
    onDragStart(event) {
      event.dataTransfer.setData('text/plain', `${this.id}`);
      event.dataTransfer.effectAllowed = 'move';
    },

    showContextMenu(event) {
      const rect = event.currentTarget.getBoundingClientRect();

      this.contextMenuX = event.clientX - rect.left;
      this.contextMenuY = event.clientY - rect.top;
      this.contextMenuVisible = true;
    },

    selectImage(imageUrl) {
      this.$emit('update:image', imageUrl, this.id);
      this.contextMenuVisible = false;
    },

    hideContextMenu() {
      this.contextMenuVisible = false;
    },

    duplicateBlock() {
      this.$emit('duplicate', this.id, this.image);
    },

    deleteBlock() {
      this.$emit('delete', this.id);
    },
  },

  mounted() {
    document.addEventListener('click', this.hideContextMenu);
  },
};
</script>