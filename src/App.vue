<template>
  <div class="flex justify-between items-center bg-white p-8">
    <h1 class="text-2xl font-bold text-gray-700">Landing Page Builder</h1>

    <div class="lp-buttons">
      <button @click="save" class="bg-gray-700 text-white px-4 py-2 rounded">
        Save
      </button>
    </div>
  </div>

  <div class="container mx-auto">
    <div class="flex">
      <div class="lp-blocks relative mb-4 h-screen p-8">
        <div class="flex flex-col space-y-2">
          <button
              @click="addTextBlock"
              class="bg-white text-gray-800 px-4 py-2 rounded"
          >
            Add Text Block
          </button>
          <button
              @click="toggleImageMenu"
              class="bg-white text-gray-800 px-4 py-2 rounded"
          >
            Add Image Block
          </button>
        </div>

        <div
            v-if="imageMenuVisible"
            class="absolute bg-white border rounded shadow mt-2 p-2 space-y-2 z-50"
            style="width: 200px;"
        >
          <p
              v-for="image in predefinedImages"
              :key="image.url"
              @click="addImageBlock(image.url)"
              class="cursor-pointer hover:bg-gray-100 p-2 rounded"
          >
            {{ image.label }}
          </p>
        </div>
      </div>

      <div class="lp-preview flex flex-col bg-white border border-gray-700 p-4">
        <component
            v-for="block in blocks"
            :is="block.type"
            :key="block.id"
            :id="block.id"
            :predefined-images="predefinedImages"
            v-bind="block.props"
            @duplicate="duplicateBlock"
            @delete="deleteBlock"
            @update:content="updateTextContent"
            @update:image="updateImageContent"
            @dragstart="onDragStart(block.id)"
            @dragenter="onDragEnter(block.id)"
            @drop="onDrop(block.id)"
            @dragover.prevent
            draggable="true"
            :class="{
              'border-2 border-blue-500': block.id === highlightedBlockId,
            }"
            class="p-2 rounded transition-all"
        />
      </div>
    </div>
  </div>
</template>

<script>
import TextBlock from "./components/TextBlock.vue";
import ImageBlock from "./components/ImageBlock.vue";

export default {
  components: {
    TextBlock,
    ImageBlock,
  },
  data() {
    return {
      blocks: [],
      nextBlockId: 1,
      draggedBlockId: null,
      highlightedBlockId: null,
      imageMenuVisible: false,
      predefinedImages: [
        { label: "Image 1", url: 'https://fastly.picsum.photos/id/12/2500/1667.jpg?hmac=Pe3284luVre9ZqNzv1jMFpLihFI6lwq7TPgMSsNXw2w'},
        { label: "Image 2", url: 'https://fastly.picsum.photos/id/16/2500/1667.jpg?hmac=uAkZwYc5phCRNFTrV_prJ_0rP0EdwJaZ4ctje2bY7aE' },
        { label: "Image 3", url: 'https://fastly.picsum.photos/id/25/5000/3333.jpg?hmac=yCz9LeSs-i72Ru0YvvpsoECnCTxZjzGde805gWrAHkM' },
      ],
    };
  },
  methods: {
    addTextBlock() {
      this.blocks.push({
        id: this.nextBlockId++,
        type: "TextBlock",
        props: {
          content: "Lorem Ipsum is simply dummy text of the printing and typesetting industry. Lorem Ipsum has been the industry's standard dummy text ever since the 1500s, when an unknown printer took a galley of type and scrambled it to make a type specimen book. It has survived not only five centuries, but also the leap into electronic typesetting, remaining essentially unchanged. It was popularised in the 1960s with the release of Letraset sheets containing Lorem Ipsum passages, and more recently with desktop publishing software like Aldus PageMaker including versions of Lorem Ipsum."
        },
      });
    },

    addImageBlock(imageUrl) {
      this.blocks.push({
        id: this.nextBlockId++,
        type: "ImageBlock",
        props: { image: imageUrl },
      });

      this.imageMenuVisible = false;
    },

    duplicateBlock(id) {
      const block = this.blocks.find((b) => b.id === id);

      if (block) {
        const newBlock = {
          ...block,
          id: this.nextBlockId++,
          props: {
            ...block.props,
            image: block.props.image
          }
        };

        this.blocks.push(newBlock);
      }
    },

    deleteBlock(id) {
      this.blocks = this.blocks.filter((block) => block.id !== id);
    },

    updateTextContent(newContent, id) {
      const block = this.blocks.find((b) => b.id === id);

      if (block) {
        block.props.content = newContent;
      }
    },

    updateImageContent(newImage, id) {
      const block = this.blocks.find((b) => b.id === id);

      if (block) {
        block.props.image = newImage;
      }
    },

    onDragStart(id) {
      this.draggedBlockId = id;
    },

    onDragEnter(targetId) {
      this.highlightedBlockId = targetId;
    },

    onDrop(targetId) {
      const draggedBlockIndex = this.blocks.findIndex(
          (block) => block.id === this.draggedBlockId
      );

      const targetBlockIndex = this.blocks.findIndex(
          (block) => block.id === targetId
      );

      if (draggedBlockIndex !== -1 && targetBlockIndex !== -1) {
        const draggedBlock = this.blocks[draggedBlockIndex];
        this.blocks.splice(draggedBlockIndex, 1);

        if (targetBlockIndex < draggedBlockIndex) {
          this.blocks.splice(targetBlockIndex, 0, draggedBlock);
        } else {
          this.blocks.splice(targetBlockIndex + 1, 0, draggedBlock);
        }
      }

      this.highlightedBlockId = null;
    },

    toggleImageMenu() {
      this.imageMenuVisible = !this.imageMenuVisible;
    },

    save() {
      const layoutData = this.blocks.map((block) => ({
        id: block.id,
        type: block.type,
        props: block.props,
      }));

      console.log(JSON.stringify(layoutData, null, 2));
    },
  },
};
</script>

<style scoped>
  .lp-blocks {
    width: 20%;
  }

  .lp-preview {
    width: 70%;
    height: 100vh;
    overflow: scroll;
  }

  .lp-buttons {
    width: 10%;
    display: flex;
    justify-content: flex-end;
  }
</style>
