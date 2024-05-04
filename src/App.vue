<script setup>
import { reactive, computed } from "vue";
import ImageUpload from "components/ImageUpload.vue";
import ImageOption from "components/ImageOption.vue";
import PairSelection from "components/PairSelection.vue";
import ProgressBar from "components/ProgressBar.vue";
import { shuffle } from "utils/shuffle-array";

const appData = reactive({
  items: [],
  pair: [],
  winners: [],
});

function addItems(items) {
  items.forEach((item) => appData.items.push(item));
  shuffle(appData.items);

  for (const item of appData.items) {
    item.rankings = [];
    for (const other of appData.items) {
      item.rankings.push({
        item: other,
        rank: item === other ? 0 : null,
      });
    }
    updateItem(item);
    shuffle(item.rankings);
  }

  selectPair();
}

function selectBestPair() {
  const list = appData.items;

  list.sort((a, b) => {
    if (a.comparisons != b.comparisons) {
      return a.comparisons - b.comparisons;
    }

    if (a.score != b.score) {
      return b.score - a.score;
    }

    return 0;
  });

  for (const item of list) {
    for (const other of list) {
      const rank = item.rankings.find((ranking) => ranking.item === other).rank;
      if (rank === null) {
        return [item, other];
      }
    }
  }
}

function selectPair() {
  const pair = selectBestPair();
  appData.pair.length = 0;

  if (pair) {
    appData.pair.push(pair[0], pair[1]);
  } else {
    selectWinners();
  }
}

function selectWinners() {
  appData.items.forEach(
    (item) =>
      (item.score = item.rankings.reduce((acc, curr) => (acc += curr.rank), 0))
  );

  appData.items.sort((a, b) => b.score - a.score);

  const maxScore = appData.items[0].score;

  for (const item of appData.items) {
    if (item.score === maxScore) {
      appData.winners.push(item);
    }
  }
}

const ranking = computed(() => {
  const items = [...appData.items];
  items.sort((a, b) => a.score - b.score).reverse();
  return items;
});

function updateItem(item) {
  item.comparisons = item.rankings.reduce(
    (acc, curr) => (acc += typeof curr.rank === "number" ? 1 : 0),
    -1
  );
  item.wins = item.rankings.reduce((acc, curr) => (acc += curr.rank || 0), 0);
  item.score = item.comparisons === 0 ? 1 : item.wins / item.comparisons;
}

function selectItem(winner) {
  let loser;

  if (winner === appData.pair[0]) {
    loser = appData.pair[1];
  } else {
    winner = appData.pair[1];
    loser = appData.pair[0];
  }

  winner.rankings.find((ranking) => ranking.item === loser).rank = 1;
  loser.rankings.find((ranking) => ranking.item === winner).rank = 0;

  updateItem(winner);
  updateItem(loser);

  selectPair();
}
</script>

<template>
  <div class="app">
    <template v-if="appData.items.length === 0">
      <ImageUpload :app-data="appData" @done="addItems" />
      <span class="app__note">
        Note: The selected images are <b><u>not uploaded</u></b> to a server,
        and will be kept locally in your browser.
      </span>
    </template>
    <template v-if="appData.pair.length > 0">
      <ProgressBar :app-data="appData" />
      <PairSelection
        :app-data="appData"
        :pair="appData.pair"
        @select="selectItem"
      />
    </template>
    <div class="winner" v-if="appData.winners.length > 0">
      <span class="winner__title" v-if="appData.winners.length === 1">
        Winner!
      </span>
      <span class="winner__title" v-else> Winners! </span>
      <div class="winner__images">
        <ImageOption
          :app-data="appData"
          :item="item"
          v-for="item in appData.winners"
        />
      </div>
    </div>
    <div class="winner__rankings">
      <ImageOption
        class="winner__loser"
        :app-data="appData"
        :item="item"
        v-for="item in ranking"
      />
    </div>
  </div>
</template>

<style scoped lang="scss">
.app {
  display: flex;
  align-items: center;
  justify-content: center;
  min-height: 100vh;
  flex-direction: column;

  &__note {
    padding: 32px;
    font-family: monospace;
  }
}
.winner {
  align-items: center;
  display: flex;
  flex-direction: column;

  &__title {
    font-family: monospace;
    font-size: 24px;
    font-weight: bold;
    padding: 32px;
    text-align: center;
  }

  &__images {
    display: flex;
    justify-content: center;
    max-width: 90vw;
  }

  &__rankings {
    display: flex;
    flex-direction: row;
    flex-wrap: wrap;
    justify-content: center;
  }

  &__loser {
    max-width: 250px;
  }
}
</style>
