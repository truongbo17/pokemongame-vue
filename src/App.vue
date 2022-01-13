<template>
  <main-screen
    v-if="statusMatch == 'default'"
    @onStart="onHanldeBeforeStart($event)"
  />
  <interact-screen
    v-if="statusMatch == 'match'"
    :cardsContext="settings.cardsContext"
    @onFinish="onResult"
  />
  <result-screen
    v-if="statusMatch == 'result'"
    :timer="timer"
    @onStartAgain="statusMatch == 'default'"
  />
  <copyright-screen />
</template>

<script>
//import component
import MainScreen from "./components/MainScreen.vue";
import InteractScreen from "./components/InteractScreen.vue";
import ResultScreen from "./components/ResultScreen.vue";
import CopyrightScreen from "./components/CopyrightScreen.vue";

//sort array
import { shuffled } from "./utils/array";

export default {
  name: "App",
  components: {
    MainScreen,
    InteractScreen,
    ResultScreen,
    CopyrightScreen,
  },
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
    onHanldeBeforeStart(config) {
      //->must load data first
      //set total block of this game
      this.settings.totalOfBlocks = config.totalOfBlocks;

      //create the first array with 16 elements
      const firstCards = Array.from(
        {
          length: this.settings.totalOfBlocks / 2,
        },
        (_, i) => i + 1
      );

      //copy array from first array
      const secondCards = [...firstCards];

      //merge array
      const cards = [...firstCards, ...secondCards];

      this.settings.cardsContext = shuffled(
        shuffled(shuffled(shuffled(cards)))
      ); //mix 4 lần

      //set time to start game
      const timeNow = new Date();
      this.settings.startedAt = timeNow.getTime(); //lấy được thời gian khi bắt đầu -> ms

      //->then load view
      this.statusMatch = "match";
    },
    onResult() {
      //lấy thời gian kết thúc - thời gian bắt đầu = thời gian làm
      const timeNow = new Date();
      this.timer = timeNow.getTime() - this.settings.startedAt;

      //chuyển tời màn hình thông báo kết quả
      this.statusMatch = "result";
    },
  },
};
</script>

<style></style>
