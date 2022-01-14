<template>
  <div class="row" id="scoreCard">
    <div class="col align-self-center">
      Score: <span id="scoreSpan">{{ Math.round(score) }}</span>
    </div>
    <div class="col align-self-center">
      Highscore: <span id="scoreSpan">{{ Math.round(highscore) }}</span>
    </div>
  </div>
  <div class="game">
    <div id="char" :class="{}" :style="{ left: x + 'px', top: y + 'px' }"></div>
    <div
      :id="Enemy.id"
      :class="Enemy.size"
      v-for="Enemy of Enemies"
      :key="Enemy"
      :style="{
        left: Enemy.x + 'px',
        top: Enemy.y + 'px',
        backgroundColor: Enemy.color,
      }"
    ></div>
    <div
      :class="item.type"
      v-for="item of items"
      :key="item"
      :style="{
        left: item.cords[0] + 'px',
        top: item.cords[1] + 'px',
      }"
    ></div>

    <button
      id="btn"
      @click="start()"
      :class="{ startBtn: true == gameStarted }"
      class="btn btn-success align-self-center shadow-none"
    >
      Start Game
    </button>
  </div>
</template>

<script lang="ts">
import { defineComponent } from "vue";
interface Enemy {
  x: number;
  y: number;
  size: string;
  id: string;
  type: string;
  color: string;
  moveVektor: number[];
}
interface Sizes {
  eSmall: 15;
  eMedium: 20;
  eBig: 25;
}
interface Item {
  type: string;
  cords: number[];
  size: string;
  timer: number;
}
export default defineComponent({
  data() {
    return {
      borderRight: 0,
      borderLeft: 0,
      borderUp: 0,
      borderDown: 0,
      gameStarted: false,
      difficulty: 1,
      highscore: 0,
      score: 0,
      x: 0,
      y: 0,
      gameloopCounter: 0,
      positionvar: 0,
      items: [] as Item[],
      pressedKeys: {} as Record<string, boolean>,
      Enemies: [] as Enemy[],
    };
  },
  computed: {
    checkDeath() {
      return false;
    },
  },
  mounted() {
    window.addEventListener("resize", () => {
      this.changeDisplaySize();
    });
    setInterval(() => {
      this.gameStarted ? this.gameloop() : null;
    }, 1000 / 60);
    this.borderRight = (window.innerWidth * (100 - 100 / 6)) / 100 - 2;
    this.borderLeft = (window.innerWidth * (100 / 6)) / 100 + 2;
    this.borderUp = window.innerHeight - 793;
    this.borderDown = window.innerHeight - (window.innerHeight - 705);
    this.y = this.borderDown - this.borderUp * 1.5;
    this.x = this.borderRight - this.borderLeft * 2;
  },
  methods: {
    gameloop() {
      this.handlePlayerMovement();
      this.handleEnemyMovement();
      this.score += this.difficulty;
      this.colisionHandling();
      this.despawnItems();
      this.gameloopCounter++;
      this.gameloopCounter % 60 == 0 ? this.handleEnemyColorSwitch() : null; //1sek
      this.gameloopCounter % 420 == 0 ? this.spawnItems() : null; //7sek
      this.gameloopCounter % 1200 == 0 ? (this.difficulty += 0.5) : null; //20sek
      this.gameloopCounter % 1800 == 0 ? this.createEnemy() : null; //30sek
    },
    colisionHandling() {
      let map = {
        eSmall: 15,
        eMedium: 20,
        eBig: 25,
      };
      for (let item of this.items) {
        if (
          Math.sqrt(
            (item.cords[0] +
              map[item.size as keyof Sizes] / 2 -
              (this.x + 7.5)) **
              2 +
              (item.cords[1] +
                map[item.size as keyof Sizes] / 2 -
                (this.y + 7.5)) **
                2
          ) <
          map[item.size as keyof Sizes] / 2 + 7.5
        ) {
          switch (item.type) {
            case "coin":
              this.items.splice(
                this.items.findIndex((i) => i == item),
                1
              );
              this.collectCoin();
              break;
            case "bomb":
              break;
          }
        }
      }
      for (let enemy of this.Enemies) {
        if (
          Math.sqrt(
            (enemy.x + map[enemy.size as keyof Sizes] / 2 - (this.x + 7.5)) **
              2 +
              (enemy.y + map[enemy.size as keyof Sizes] / 2 - (this.y + 7.5)) **
                2
          ) <
          map[enemy.size as keyof Sizes] / 2 + 7.5
        ) {
          this.gameStarted = false;
          this.score > this.highscore ? (this.highscore = this.score) : null;
        }
      }
    },
    collectCoin() {
      this.score += this.difficulty * 300;
    },
    explosionBomb() {
      return true;
    },
    despawnItems() {
      for (let item of this.items) {
        item.timer--;
        item.timer < 0
          ? this.items.splice(
              this.items.findIndex((i) => i == item),
              1
            )
          : null;
      }
    },
    spawnItems() {
      let type = "";
      let cords = [] as number[];
      switch (this.getRandomInt(2)) {
        case 0:
          type = "coin";
          break;
        case 1:
          type = "bomb";
          break;
      }
      cords[0] =
        this.getRandomInt(this.borderRight - this.borderLeft) + this.borderLeft;
      cords[1] =
        this.getRandomInt(this.borderDown - this.borderUp) + this.borderUp;
      this.items.push({
        type: type,
        cords: cords,
        size: "eMedium",
        timer: 300, //5sek
      });
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
    handleEnemyColorSwitch() {
      for (let enemy of this.Enemies) {
        if (enemy.type == "colorswitch") {
          switch (this.getRandomInt(3)) {
            case 0:
              enemy.color = "rgb(99, 206, 50)";
              break;
            case 1:
              enemy.color = "rgb(50, 206, 198)";
              break;
            case 2:
              enemy.color = "rgb(84, 50, 206)";
              break;
          }
        }
      }
    },
    handleEnemyMovement() {
      for (let enemy of this.Enemies) {
        if (enemy.type == "curve") {
          enemy.moveVektor[enemy.moveVektor.findIndex((v) => v != 1)] += 0.02;
        }
        enemy.x += enemy.moveVektor[0] * this.difficulty;
        enemy.y += enemy.moveVektor[1] * this.difficulty;

        if (enemy.y < this.borderUp - 25 || enemy.y > this.borderDown + 25) {
          this.Enemies.splice(
            this.Enemies.findIndex((e) => e == enemy),
            1
          );
          this.createEnemy();
        }
        if (enemy.x < this.borderLeft - 25 || enemy.x > this.borderRight + 25) {
          this.Enemies.splice(
            this.Enemies.findIndex((e) => e == enemy),
            1
          );
          this.createEnemy();
        }
      }
    },
    createEnemy() {
      let size = "";
      let x = 0;
      let y = 0;
      let type = "";
      let color = "";
      let moveArray = [] as number[];
      switch (this.getRandomInt(4)) {
        case 0:
          y = this.borderUp - 25;
          moveArray = [(Math.random() - 0.5) * 2, 1];
          break;
        case 1:
          y = this.borderDown + 25;
          moveArray = [(Math.random() - 0.5) * 2, -1];
          break;
        case 2:
          x = this.borderRight + 25;
          moveArray = [-1, (Math.random() - 0.5) * 2];
          break;
        case 3:
          x = this.borderLeft - 25;
          moveArray = [1, (Math.random() - 0.5) * 2];
          break;
      }
      if (!x) {
        x = this.getRandomInt(this.borderRight - this.borderLeft);
      }
      if (!y) {
        y = this.getRandomInt(this.borderDown - this.borderUp);
      }
      switch (this.getRandomInt(3)) {
        case 0:
          size = "eSmall";
          color = "rgb(99, 206, 50)";
          break;
        case 1:
          size = "eMedium";
          color = "rgb(50, 206, 198)";
          break;
        case 2:
          size = "eBig";
          color = "rgb(84, 50, 206)";
          break;
      }
      switch (this.getRandomInt(2)) {
        case 0:
          type = "curve";
          break;
        case 1:
          type = "colorswitch";
          break;
      }
      this.Enemies[this.Enemies.length] = {
        x: x,
        y: y,
        size: size,
        id: JSON.stringify(this.getRandomInt(100000000)),
        type: type,
        color: color,
        moveVektor: moveArray,
      };
    },
    getRandomInt(max: number) {
      return Math.floor(Math.random() * max);
    },
    start() {
      this.score = 0;
      this.difficulty = 2;
      this.y = this.borderDown - this.borderUp * 1.5;
      this.x = this.borderRight - this.borderLeft * 2;
      this.Enemies = [] as Enemy[];
      this.items = [] as Item[];
      this.gameStarted = true;
      window.onkeyup = (e: any) => {
        this.pressedKeys[e.key] = false;
      };
      window.onkeydown = (e: any) => {
        this.pressedKeys[e.key] = true;
      };
      for (let i = 0; i < 8; i++) this.createEnemy();
      console.log("game started");
    },
    up() {
      if (this.y > this.borderUp) {
        this.y -= 5;
        this.y < this.borderUp ? (this.y = this.borderUp) : null;
      }
    },
    down() {
      if (this.y < this.borderDown) {
        this.y += 5;
        this.y > this.borderDown + 15 ? (this.y = this.borderDown + 15) : null;
      }
    },
    right() {
      if (this.x < this.borderRight) {
        this.x += 5;
        this.x > this.borderRight - 15
          ? (this.x = this.borderRight - 15)
          : null;
      }
    },
    left() {
      if (this.x > this.borderLeft) {
        this.x -= 5;
        this.x < this.borderLeft - 1 ? (this.x = this.borderLeft - 1) : null;
      }
    },
    changeDisplaySize() {
      this.borderRight = (window.innerWidth * (100 - 100 / 6)) / 100 - 2;
      this.borderLeft = (window.innerWidth * (100 / 6)) / 100 + 2;
      this.borderUp = window.innerHeight - 793;
      this.borderDown = window.innerHeight - (window.innerHeight - 705);
    },
  },
});
</script>

<style lang="scss" scoped>
.game {
  // widht=1280px
  width: 100%;
  height: 550px;
  border: 2px solid black;
  background-color: rgb(255, 255, 255);
  z-index: 1;
}
#scoreCard {
  background-color: white;
  position: relative;
  z-index: 1;
}
#char {
  position: absolute;
  border-radius: 50%;
  width: 15px;
  height: 15px;
  background-color: red;
}
.eSmall {
  position: absolute;
  border-radius: 50%;
  width: 15px;
  height: 15px;
  background-color: rgb(99, 206, 50);
}
.eMedium {
  position: absolute;
  border-radius: 50%;
  width: 20px;
  height: 20px;
  background-color: rgb(50, 206, 198);
}
.eBig {
  position: absolute;
  border-radius: 50%;
  width: 25px;
  height: 25px;
  background-color: rgb(84, 50, 206);
}
.coin {
  position: absolute;
  border-radius: 50%;
  width: 25px;
  height: 25px;
  background-color: rgb(199, 219, 15);
}
.bomb {
  position: absolute;
  border-radius: 50%;
  width: 25px;
  height: 25px;
  background-color: rgb(0, 0, 0);
}
.sideBlock {
  background-color: red;
  z-index: 1;
  position: fixed;
}
.block {
  width: 100%;
  height: 30px;
  z-index: 1;
  background-color: blue;
  position: fixed;
}
.startBtn {
  display: none;
}
.btn {
  padding: none !important;
}
</style>
