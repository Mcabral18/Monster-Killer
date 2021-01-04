<template>
  <div id="app">
    <div class="panel scores">
      <div class="score">
        <h1>Jogador</h1>
        <div class="life-bar">
          <div
            class="life"
            :class="{ danger: player < 20 }"
            :style="{ width: player + '%' }"
          >
            <!-- percentage of damege -->
          </div>
          <div class="data_bind">{{ player }}%</div>
        </div>
      </div>
      <div class="score">
        <h1>Monster</h1>
        <div class="life-bar">
          <div
            class="life"
            :class="{ danger: monster < 20 }"
            :style="{ width: monster + '%' }"
          >
            <!-- percentage of damage -->
          </div>
          <div class="data_bind">{{ monster }}%</div>
        </div>
      </div>
    </div>
    <div v-if="Result" class="panel result">
      <div v-if="monster == 0" class="win">You win</div>
      <div v-else class="lose">You lose</div>
      <!-- <div v-show="monster == 0" class="win">You win</div>
      <div v-show="player == 0" class="lose">You lose</div> -->
    </div>
    <div class="panel buttons">
      <template v-if="running">
        <button class="btn attack" @click="attack(false)">Attack</button>
        <button class="btn special" @click="attack(true)">
          Special Attack
        </button>
        <button class="btn heal" @click="healAndHurt">Heal</button>
        <button class="btn quit" @click="running = false">Quit</button>
      </template>
      <button v-else class="btn start" @click="StartGame">Start Game</button>
    </div>
    <div v-if="running" class="panel logs">
      <ul>
        <li v-for="(log, index) in logs" :key="index" :class="log.cls">
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
      running: false,
      player: 100,
      monster: 100,
      logs: [],
    };
  },
  computed: {
    Result() {
      return this.player == 0 || this.monster == 0;
    },
  },
  methods: {
    StartGame() {
      this.running = true;
      this.player = 100;
      this.monster = 100;
      this.logs = [];
    },
    attack(especial) {
      if (this.monster > 0) {
        this.hurt("player", 7, 12, false, "Monster", "Jogador", "monster");
      }
      this.hurt("monster", 5, 10, especial, "Jogador", "Monster", "player");
    },
    hurt(prop, min, max, especial, source, target, cls) {
      const plus = especial ? 5 : 0;
      const hurt = this.getRandom(min + plus, max + plus);
      this[prop] = Math.max(this.player - hurt, 0);
      this.registerLog(`${source} hit ${target} with ${hurt}`, cls);
    },
    // Get random values
    getRandom(min, max) {
      const value = Math.random() * (max - min) + min;
      return Math.round(value);
    },
    healAndHurt() {
      this.heal(10, 15);
      this.hurt("player", 7, 12, false, "Monster", "Jogador", "monster");
    },
    heal(min, max) {
      const heal = this.getRandom(min, max);
      this.player = Math.min(this.player + heal, 100);
      this.registerLog(`Jogador win ${heal} life.`, "player");
    },
    registerLog(text, cls) {
      //cls monster and player style
      this.logs.unshift({ text, cls });
    },
  },
  watch: {
    Result(value) {
      if (value) this.running = false;
    },
  },
};
</script>

<style src="./styles/style.css"></style>