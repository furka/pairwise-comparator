<script setup>
const props = defineProps({
  appData: Object,
  item: Object,
});
</script>

<template>
  <div class="image-option">
    <img class="image-option__image" :src="item.image.src" />
    <span class="image-option__name"> {{ item.name }}</span>
    <span class="image-option__rank">
      wins: {{ item.wins }} / {{ item.comparisons }}</span
    >
    <span class="image-option__rank"> score: {{ item.score }}</span>
  </div>
</template>

<style scoped lang="scss">
.image-option {
  align-items: center;
  background-color: white;
  border-radius: 8px;
  border: 3px solid #aaa;
  display: flex;
  flex-direction: column;
  padding: 8px;

  &__image {
    max-height: 80vh;
    max-width: 100%;
    object-fit: contain;
  }

  &__name {
    font-family: monospace;
  }

  &__rank {
    font-family: monospace;
  }
}
</style>
