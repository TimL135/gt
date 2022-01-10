<template>
  <div
    class="game"
    tabindex="0"
    @keypress.w="up()"
    @keypress.s="down()"
    @keypress.d="right()"
    @keypress.a="left()"
  >
    <div id="char" :class="{}"></div>
    <div :class="{ blockLow: true == gameStarted && 0 == positionvar }"></div>
    <div :class="{ blockHigh: true == gameStarted && 1 == positionvar }"></div>
  </div>
  <button id="btn" @click="start()" :class="{ startBtn: true == gameStarted }">
    Start Game
  </button>
  <p>Score: <span id="scoreSpan"></span></p>
</template>

<script lang="ts">
import { defineComponent } from "vue";

export default defineComponent({
  data() {
    return {
      gameStarted: false,
      x: 254,
      y: 104,
      positionvar: 0,
    };
  },
  computed: {
    checkDeath() {
      return false;
    },
  },
  methods: {
    position() {
      setInterval(() => (this.positionvar = this.getRandomInt(2)), 1600);
    },
    getRandomInt(max: number) {
      return Math.floor(Math.random() * max);
    },
    start() {
      this.gameStarted = true;
      this.position();
      console.log("game started");
    },
    up() {
      if (this.y > 105) {
        this.y -= 15;
        this.y < 104 ? (this.y = 104) : null;
        var d = document.getElementById("char");
        d!.style.position = "absolute";
        d!.style.left = this.x + "px";
        d!.style.top = this.y + "px";
        console.log("up");
      }
    },
    down() {
      if (this.y < 638) {
        this.y += 15;
        this.y > 639 ? (this.y = 639) : null;
        var d = document.getElementById("char");
        d!.style.position = "absolute";
        d!.style.left = this.x + "px";
        d!.style.top = this.y + "px";
        console.log("down");
      }
    },
    right() {
      if (this.x < 790) {
        this.x += 15;
        this.x > 789 ? (this.x = 789) : null;
        var d = document.getElementById("char");
        d!.style.position = "absolute";
        d!.style.left = this.x + "px";
        d!.style.top = this.y + "px";
        console.log("right");
      }
    },
    left() {
      if (this.x > 255) {
        this.x -= 15;
        this.x < 254 ? (this.x = 254) : null;
        var d = document.getElementById("char");
        d!.style.position = "absolute";
        d!.style.left = this.x + "px";
        d!.style.top = this.y + "px";
        console.log("left");
      }
    },
  },
});
</script>

<style lang="scss" scoped>
.game {
  width: 550px;
  height: 550px;
  border: 2px solid black;
  margin: auto;
}
#btn {
  position: relative;
  top: 110px;
}
.startBtn {
  visibility: hidden;
}
#char {
  width: 15px;
  height: 15px;
  background-color: red;
}
</style>
