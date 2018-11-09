<template>
  <div style="text-align:center"><br/>
    <b-card header-tag="header"
            footer-tag="footer"
            bg-variant="dark"
            text-variant="white" class="justify-content-md-center" align-self="center">
        <h6 slot="header" class="mb-0">Score: {{ score }}</h6>
        <div style="font-size:2em; text-align:center">
            <p>{{ this.x }} {{ this.operator }} {{ this.y }}</p>
            <p>{{ this.equals }} {{ this.z }}</p>
            <p>{{ this.message }}</p>
        </div>
        <div slot="footer">
        <b-progress :animated="this.interval" :value="countdown"></b-progress><br/>
        <b-button variant="success lg" class="btnChoice" v-on:click="choiceTrue()">✔</b-button>&nbsp;
        <b-button variant="danger lg" class="btnChoice" v-on:click="choiceFalse()">X</b-button>
        </div>
    </b-card>
    <br/>
    <b-button variant="info lg" v-on:click="reset()" v-show="this.countdown == 0">Restart</b-button>
    <b-navbar fixed="bottom" variant="info" type="dark">
      <b-navbar-brand class="mx-auto">
        [◄] Equation true | [►] Equation false | [▼] Restart
      </b-navbar-brand>
    </b-navbar>
  </div>
</template>

<script>
const GameMode = {
  Start: 1,
  Playing: 2,
  GameOver: 3
};

const TermRandomRange = {
  Min: 1,
  Max: 15
};

const HiddenRandomRange = {
  Min: 1,
  Max: 3
};

export default {
  name: "Game",
  data() {
    return {
      score: 0,
      x: 0,
      y: 0,
      z: 0,
      operator: "+",
      equals: "=",
      countdown: 0,
      interval: 0,
      message: "",
      mode: GameMode.Start,
      audioScore: new Audio(require("../assets/score.wav")),
      audioGameOver: new Audio(require("../assets/gameover.wav"))
    };
  },
  methods: {
    getRandomInt(min, max) {
      return Math.floor(Math.random() * (max - min + 1)) + min;
    },
    initGame(start) {
      this.x = this.getRandomInt(TermRandomRange.Min, TermRandomRange.Max);
      this.y = this.getRandomInt(TermRandomRange.Min, TermRandomRange.Max);

      // Random value whether the equation should be true
      var equationTrue = new Boolean(this.getRandomInt(0, 1));
      // What is added to make the equation false?
      var hidden =
        equationTrue == false
          ? this.getRandomInt(HiddenRandomRange.Min, HiddenRandomRange.Max)
          : 0;
      // Result of the equation including the optional hidden value
      var result = this.x + this.y + hidden;

      this.z = result;
      this.countdown = 100;
      if (!start) {
        if (!this.interval) {
          this.mode = GameMode.Playing;
          this.interval = setInterval(() => {
            if (this.countdown == 0) {
              this.gameOver();
              return;
            }
            this.countdown = this.countdown - 20;
          }, 200);
        }
      }
    },
    gameOver() {
      this.audioGameOver.play();
      clearInterval(this.interval);
      this.x = "";
      this.y = "";
      this.z = "";
      this.operator = "";
      this.equals = "";
      this.message = "GAME OVER";
      this.mode = GameMode.GameOver;
      this.countdown = 0;
    },
    choiceTrue() {
      if (this.mode == GameMode.GameOver) return;
      if (this.z === this.x + this.y) {
        this.userChoiceCorrect();
      } else {
        this.gameOver();
      }
    },
    choiceFalse() {
      if (this.mode == GameMode.GameOver) return;
      if (this.z !== this.x + this.y) {
        this.userChoiceCorrect();
      } else {
        this.gameOver();
      }
    },
    userChoiceCorrect() {
      this.score++;
      this.audioScore.play();
      this.initGame(false);
    },
    reset() {
      if (this.mode == GameMode.GameOver) {
        // Hack to reset the component
        Object.assign(this.$data, this.$options.data());
        this.initGame(true);
      }
    }
  },
  mounted() {
    this.initGame(true);

    window.onkeydown = event => {
      // Arrow left
      if (event.keyCode == 37) this.choiceTrue();
      // Arrow right
      if (event.keyCode == 39) this.choiceFalse();
      // Arrow down
      if (event.keyCode == 40) this.reset();
    };
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.btnChoice {
  width: 100px;
  height: 100px;
  text-align: center;
  padding-top: 0.6em;
  font-size: 2em;
}
</style>
