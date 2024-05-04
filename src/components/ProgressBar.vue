<script setup>
import { computed } from "vue";
const props = defineProps({
  appData: Object,
});

const maxItems = computed(() => {
  return (
    props.appData.items.length * props.appData.items.length -
    props.appData.items.length
  );
});

const doneItems = computed(() => {
  let done = 0;

  props.appData.items.forEach((item) =>
    item.rankings.forEach((ranking) => {
      if (typeof ranking.rank === "number") {
        done += 1;
      }
    })
  );

  return done - props.appData.items.length;
});

const width = computed(() => {
  return `${(doneItems.value / maxItems.value) * 100}%`;
});
</script>

<template>
  <div class="progress-bar">
    <div class="progress-bar__fill"></div>
  </div>
</template>

<style scoped lang="scss">
.progress-bar {
  box-sizing: border-box;
  position: relative;
  width: 100%;
  height: 20px;
  border: 3px solid black;
  border-radius: 20px;

  &__fill {
    background-color: #0f0;
    border-radius: 20px;
    width: v-bind("width");
    position: absolute;
    top: 0;
    left: 0;
    bottom: 0;
  }
}
</style>
