<script setup>
import { ref } from "vue";
const input = ref(null);

const emit = defineEmits(["done"]);

const props = defineProps({
  appData: Object,
});

async function addFile(file) {
  const reader = new FileReader();
  const image = new Image();

  return await new Promise((resolve) => {
    reader.onload = (e) => {
      image.src = reader.result;
      resolve({
        image,
        name: file.name,
      });
    };
    reader.readAsDataURL(file);
  });
}

async function onChange(e) {
  const items = await Promise.all(
    Array.from(e.target.files).map((file) => addFile(file))
  );
  emit("done", items);
}
</script>

<template>
  <div class="image-upload">
    <button class="image-upload__button" @click="input.click">
      Choose Images
    </button>
    <input
      ref="input"
      class="image-upload__input"
      type="file"
      accept="image/*"
      multiple="true"
      @input="onChange"
    />
  </div>
</template>

<style scoped lang="scss">
.image-upload {
  &__button {
    border: 3px solid black;
    border-radius: 16px;
    padding: 16px;
    font-family: monospace;
    font-size: 48px;
    font-weight: bold;
    cursor: pointer;
  }

  &__input {
    display: none;
  }
}
</style>
