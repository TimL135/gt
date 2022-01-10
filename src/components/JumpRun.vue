<template>
  <div class="game" tabindex="0">
    <div id="char" :class="{}"></div>
    <div
      :id="Enemy.id"
      :class="Enemy.size"
      v-for="Enemy of Enemies"
      :key="Enemy"
    ></div>
  </div>
  <button id="btn" @click="start()" :class="{ startBtn: true == gameStarted }">
    Start Game
  </button>
  <p>Score: <span id="scoreSpan"></span></p>
</template>

<script lang="ts">
import { defineComponent } from "vue";
interface Enemy {
  x: number;
  y: number;
  size: string;
  id: string;
  moveVektor: number[];
}

export default defineComponent({
  data() {
    return {
      gameStarted: false,
      x: 254,
      y: 104,
      positionvar: 0,
      pressedKeys: {} as Record<string, boolean>,
      Enemies: [] as Enemy[],
      // Enemy: {
      //   x: 0,
      //   y: 0,
      //   size: "eSmall",
      //id:""
      // },
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
      this.handleEnemyMovement();
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
    handleEnemyMovement() {
      for (let enemy of this.Enemies) {
        enemy.x += enemy.moveVektor[0];
        enemy.y += enemy.moveVektor[1];
        let enemyDiv = document.getElementById(enemy.id);
        enemyDiv!.style.position = "absolute";
        enemyDiv!.style.left = enemy.x + "px";
        enemyDiv!.style.top = enemy.y + "px";
      }
    },
    createEnemy() {
      let size = "";
      let x = 0;
      let y = 0;
      let moveArray = [] as number[];
      switch (this.getRandomInt(4)) {
        case 0:
          y = 105 - 25;
          moveArray = [(Math.random() - 0.5) * 2, 1];
          break;
        case 1:
          y = 638 + 25;
          moveArray = [(Math.random() - 0.5) * 2, -1];
          break;
        case 2:
          x = 790 + 25;
          moveArray = [-1, (Math.random() - 0.5) * 2];
          break;
        case 3:
          x = 255 - 25;
          moveArray = [1, (Math.random() - 0.5) * 2];
          break;
      }

      if (!x) {
        x = this.getRandomInt(534);
      }
      if (!y) {
        y = this.getRandomInt(534);
      }
      switch (this.getRandomInt(3)) {
        case 0:
          size = "eSmall";
          break;
        case 1:
          size = "eMedium";
          break;
        case 2:
          size = "eBig";
          break;
      }
      this.Enemies[this.Enemies.length] = {
        x: x,
        y: y,
        size: size,
        id: JSON.stringify(this.getRandomInt(100000000)),
        moveVektor: moveArray,
      };
    },
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
      this.createEnemy();
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
.eSmall {
  width: 15px;
  height: 15px;
  background-color: rgb(99, 206, 50);
}
.eMedium {
  width: 20px;
  height: 20px;
  background-color: rgb(50, 206, 198);
}
.eBig {
  width: 25px;
  height: 25px;
  background-color: rgb(84, 50, 206);
}
</style>
