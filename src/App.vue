<template>
  <main-screen
    v-if="statusMatch === 'default'"
    @onStart="onHandleBeforeStart($event)"
  ></main-screen>
  <interact-screen
    v-if="statusMatch === 'match'"
    :cardsContext="settings.cardsContext"
    @onFinish="onGetResult()"
    @onStartAgain="statusMatch = 'default'"
  />
  <result-screen
    v-if="statusMatch === 'result'"
    :level="`level-${settings.totalOfBlocks}`"
    :timer="timer"
    @onStartAgain="statusMatch = 'default'"
  />
  <copy-right-screen />
</template>

<script>
import MainScreen from "./components/MainScreen.vue";
import InteractScreen from "./components/InteractScreen.vue";
import ResultScreen from "./components/ResultScreen.vue";
import CopyRightScreen from "./components/CopyRightScreen.vue";
import { suffled } from "./assets/utils/array.js";
export default {
  components: {
    MainScreen,
    InteractScreen,
    ResultScreen,
    CopyRightScreen,
  },
  name: "App",
  data() {
    return {
      settings: {
        totalOfBlocks: 0,
        cardsContext: [],
        startedAt: null,
      },
      statusMatch: "default",
      timer: 0,
    };
  },
  methods: {
    onHandleBeforeStart(config) {
      console.log("running handle before start, ", config);
      this.settings.totalOfBlocks = config.totalOfBlocks;
      if (
        localStorage.getItem(`level-${this.settings.totalOfBlocks}`) == null
      ) {
        localStorage.setItem(
          `level-${this.settings.totalOfBlocks}`,
          1000000000
        );
      }
      const firstCards = Array.from(
        {
          length: this.settings.totalOfBlocks / 2,
        },
        (_, i) => i + 1
      );
      // const secondCards = Array.from(firstCards);
      const secondCards = [...firstCards];
      const cards = [...firstCards, ...secondCards];
      this.settings.cardsContext = suffled(cards);
      console.log(this.settings.cardsContext);

      this.settings.startedAt = new Date().getTime();
      // data ready
      this.statusMatch = "match";
    },
    onGetResult() {
      // get timer
      this.timer =
        (new Date().getTime() - this.settings.startedAt - 920) / 1000;

      var highScore = localStorage.getItem(
        `level-${this.settings.totalOfBlocks}`
      );
      if (this.timer < highScore) {
        localStorage.setItem(
          `level-${this.settings.totalOfBlocks}`,
          this.timer
        );
      }
      this.statusMatch = "result";
    },
  },
};
</script>

<style></style>
