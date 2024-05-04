<script setup>
import ImageOption from "components/ImageOption.vue";
const emit = defineEmits(["select"]);

const props = defineProps({
  appData: Object,
  pair: Array,
});

function select(item) {
  emit("select", item);
}
</script>

<template>
  <div class="pair-selection">
    <span class="pair-selection__header"> Which do you prefer?</span>
    <div class="pair-selection__choices">
      <ImageOption
        class="pair-selection__option"
        :app-data="appData"
        :item="pair[0]"
        @click="select(pair[0])"
      />
      <span class="pair-selection__vs"> VS </span>
      <ImageOption
        class="pair-selection__option"
        :app-data="appData"
        :item="pair[1]"
        @click="select(pair[1])"
      />
    </div>
    <span class="pair-selection__header">
      Round: {{ pair[0].comparisons + 1 }} /
      {{ pair[0].rankings.length - 1 }}</span
    >
  </div>
</template>

<style scoped lang="scss">
.pair-selection {
  background-color: #ccc;
  border-radius: 16px;
  border: 3px solid black;
  padding: 0 32px;
  display: flex;
  flex-direction: column;
  align-items: stretch;
  justify-content: space-between;
  margin: 32px;

  &__header {
    font-family: monospace;
    font-size: 24px;
    font-weight: bold;
    padding: 32px 0;
    text-align: center;
  }

  &__choices {
    display: flex;
    flex-direction: row;
    align-items: center;
    justify-content: space-between;
  }

  &__vs {
    font-family: monospace;
    font-size: 48px;
    font-weight: bold;
    padding: 0 48px;
  }

  &__option {
    cursor: pointer;

    &:hover {
      border-color: black;
    }
  }
}
</style>
