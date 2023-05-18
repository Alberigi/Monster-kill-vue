<!-- eslint-disable vue/require-v-for-key -->
<template>
  <div id="app">
    <div class="panel scores">
      <div class="score">
        <h1>Player</h1>
        <div class="life-bar">
          <div
            class="life"
            :class="{ danger: playerLife < 20 }"
            :style="{ width: playerLife + '%' }"
          ></div>
        </div>
        <div>{{ playerLife }}%</div>
      </div>
      <div class="score">
        <h1>Monster</h1>
        <div class="life-bar">
          <div
            class="life"
            :class="{ danger: monsterLife < 20 }"
            :style="{ width: monsterLife + '%' }"
          ></div>
        </div>
        <div>{{ monsterLife }}%</div>
      </div>
    </div>
    <div v-if="hasResult" class="panel result">
      <div v-if="isPlayerWinner" class="win">You win!!!!</div>
      <div v-if="isMonsterWinner" class="lose">You lose....</div>
    </div>
    <div class="panel buttons">
      <template v-if="runnig">
        <button class="btn attack" @click="attack(false)">Attack</button>
        <button class="btn especial-attack" @click="attack(true)">
          Especial Attack
        </button>
        <button class="btn heal" @click="handlerHeal">Heal</button>
        <button class="btn give-up" @click="startGame">Give Up</button>
      </template>
      <button v-else class="btn new-game" @click="startGame">New Game</button>
    </div>
    <div v-if="logs.length" class="panel logs">
      <ul>
        <li v-for="log in logs" :class="log.cls" class="log">
          {{ log.text }}
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
export default {
  name: "App",

  data() {
    return {
      runnig: false,
      playerLife: 100,
      monsterLife: 100,
      logs: [],
    };
  },

  computed: {
    hasResult() {
      return this.playerLife === 0 || this.monsterLife === 0;
    },
    isPlayerWinner() {
      return this.monsterLife === 0;
    },
    isMonsterWinner() {
      return this.playerLife === 0;
    },
  },

  methods: {
    startGame() {
      this.playerLife = 100;
      this.monsterLife = 100;
      this.runnig = true;
    },

    endGame() {
      this.runnig = false;
    },

    attack(especial) {
      this.hurt("monsterLife", 10, 5, especial);
      this.hurt("playerLife", 12, 7, false);
    },

    hurt(source, max, min, especial) {
      const plus = especial ? 5 : 0;
      const hurt = this.getRandom(max + plus, min + plus);
      this[source] = Math.max(this.playerLife - hurt, 0);
      this.registerLog(source, hurt);
    },

    handlerHeal() {
      const heal = this.getRandom(15, 10);
      this.playerLife = Math.min(this.playerLife + heal, 100);
      this.hurt("playerLife", 12, 7, false);
      this.registerLog("heal", heal);
    },

    getRandom(max, min) {
      const value = Math.random() * (max - min) + min;
      return Math.round(value);
    },

    registerLog(source, score) {
      switch (source) {
        case "monsterLife":
          this.logs.unshift({
            text: `Player attack the Monster with ${score}`,
            cls: "player",
          });
          break;

        case "playerLife":
          this.logs.unshift({
            text: `Monster attack the Player with ${score}`,
            cls: "monster",
          });
          break;

        case "heal":
          this.logs.unshift({
            text: `Player heal with ${score}`,
            cls: "player",
          });
          break;
      }
    },
  },

  watch: {
    hasResult(value) {
      if (value) this.endGame();
    },
  },
};
</script>

<style>
html {
  font-family: sans-serif;
}

#app {
  display: flex;
  flex-direction: column;
}

.panel {
  margin: 10px;
  padding: 10px;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.15);
}

.scores {
  display: flex;
}

.score {
  flex-direction: column;
  display: flex;
  flex: 1;
  justify-content: center;
  align-items: center;
}

.score h1 {
  font-weight: 300;
  font-size: 1.6rem;
}

.life-bar {
  width: 80px;
  height: 20px;
  border: 1px solid #aaa;
}

.life-bar .life {
  display: flex;
  justify-content: center;
  height: 100%;
  background-color: green;
}

.life-bar .life.danger {
  background-color: red;
}

.result {
  display: flex;
  justify-content: center;
  font-size: 1.3rem;
  font-weight: 600;
}

.result .win {
  color: green;
}

.result .lose {
  color: red;
}

.buttons {
  display: flex;
  justify-content: center;
}

.btn {
  margin: 0px 10px;
  padding: 5px 10px;
  border-radius: 5px;
  text-transform: uppercase;
  font-size: 1.1rem;
}

.attack {
  background-color: red;
  color: white;
}
.especial-attack {
  background-color: orange;
  color: black;
}
.heal {
  background-color: green;
  color: white;
}
.give-up {
  background-color: gray;
  color: black;
}
.new-game {
  background-color: blueviolet;
  color: white;
}

.logs ul {
  display: flex;
  flex-direction: column;
  list-style: none;
  padding: 0;
  margin: 0;
}

.logs ul li {
  display: flex;
  justify-content: center;
  margin: 4px 0px;
  padding: 3px 0px;
  font-weight: 600;
  font-size: 1.1rem;
  text-transform: uppercase;
  border-radius: 3px;
}

.player {
  background: blue;
  color: white;
}

.monster {
  background: red;
  color: white;
}
</style>
