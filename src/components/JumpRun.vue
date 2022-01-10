<template>
  <div class="game" tabindex="0">
    <div id="char" :class="{}"></div>
    <div :class="{}"></div>
    <div :class="{}"></div>
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
      pressedKeys: {} as Record<string, boolean>,
    };
  },
  computed: {
    checkDeath() {
      return false;
    },
  },
  methods: {
    gameloop() {
      this.handlePlayerMovement();
    },
    handlePlayerMovement() {
      if (this.pressedKeys["ArrowDown"]) {
        this.down();
      }
      if (this.pressedKeys["ArrowLeft"]) {
        this.left();
      }
      if (this.pressedKeys["ArrowRight"]) {
        this.right();
      }
      if (this.pressedKeys["ArrowUp"]) {
        this.up();
      }
    },
    createEnemy() {},
    getRandomInt(max: number) {
      return Math.floor(Math.random() * max);
    },
    start() {
      this.gameStarted = true;
      window.onkeyup = (e: any) => {
        this.pressedKeys[e.key] = false;
      };
      window.onkeydown = (e: any) => {
        this.pressedKeys[e.key] = true;
      };
      setInterval(() => {
        this.gameloop();
      }, 1000 / 60);

      console.log("game started");
    },
    up() {
      if (this.y > 105) {
        this.y -= 5;
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
        this.y += 5;
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
        this.x += 5;
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
        this.x -= 5;
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
#enemySmall {
  width: 15px;
  height: 15px;
  background-color: rgb(99, 206, 50);
}
#enemyMedium {
  width: 20px;
  height: 20px;
  background-color: rgb(50, 206, 198);
}
#enemyBig {
  width: 25px;
  height: 25px;
  background-color: rgb(84, 50, 206);
}
</style>
