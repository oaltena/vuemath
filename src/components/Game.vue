<template>
  <div>
    <b-card header-tag="header"
            footer-tag="footer"
            bg-variant="dark"
            text-variant="white">
        <h6 slot="header" class="mb-0">Score: {{ score }}</h6>
        <div style="font-size:2em">
            <p>{{ this.x }} + {{ this.y }}</p>
            <p>= {{ this.z }}</p>
        </div>
        <div slot="footer">
        <b-progress show-progress animated :value="countdown"></b-progress><br/>
        <b-button variant="success lg" class="btnChoice" v-on:click="choiceTrue()">✔</b-button>&nbsp;
        <b-button variant="danger lg" class="btnChoice" v-on:click="choiceFalse()">X</b-button>
        </div>
    </b-card>
  </div>
</template>

<script>
export default {
  name: 'Game',
  data() {
      return {
          score: 0,
          x: 0,
          y: 0,
          z: 0,
          countdown: 0,
          timeout: {},
          interval: {}
      }
  },
  methods: {
      getRandomInt(min, max) {
          return Math.floor(Math.random() * (max - min + 1)) + min;
      },
      initGame(start) {
          this.x = this.getRandomInt(1, 20)
          this.y = this.getRandomInt(1, 20)

          // Soll die Gleichung korrekt sein?
          var termCorrect = new Boolean(this.getRandomInt(0, 1))
          // Was soll zur Gleichung addiert werden, damit sie falsch ist?
          var hidden = (termCorrect == false ? this.getRandomInt(1, 4) : 0)
          // Ergebnis der Gleichung
          var result = this.x + this.y + hidden

          this.z = result
          this.countdown = 100

          if(!start) {
            this.interval = setInterval(() => { this.countdown = this.countdown > 0 ? this.countdown - 15 : 0 }, 100)
            this.timeout = setTimeout(this.gameOver, 1500);
          }
      },
      gameOver() {
          this.countdown = 0
          clearInterval(this.interval)
          clearTimeout(this.timeout)
      },
      choiceTrue() {
          if(this.countdown <= 0)
            return
          if(this.z === (this.x + this.y)) {
              this.score++;
              this.initGame(false);
          } else {
              this.score--;
              this.initGame(false);
          }
      },
      choiceFalse() {
        if(this.countdown <= 0)
            return
        if(this.z !== (this.x + this.y)) {
            this.score++;
            this.initGame(false);
        } else {
            this.score--;
            this.initGame(false);
        }
      }
  },
  mounted() {
      this.initGame(true)
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.btnChoice {
    width:100px; 
    height:100px;
    text-align: center;
    padding-top: 0.6em;
    font-size: 2em;
}
</style>