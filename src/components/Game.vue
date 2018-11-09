<template>
  <div>
    <b-card header-tag="header"
            footer-tag="footer"
            bg-variant="dark"
            text-variant="white">
        <h6 slot="header" class="mb-0">Score: {{ score }}</h6>
        <div style="font-size:2em">
            <p>{{ this.x }} {{ this.operator }} {{ this.y }}</p>
            <p>{{ this.equals }} {{ this.z }}</p>
            <p>{{ this.message }}</p>
        </div>
        <div slot="footer">
        <b-progress :animated="animate" :value="countdown"></b-progress><br/>
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
          operator: '+',
          equals: '=',
          countdown: 0,
          interval: 0,
          animate: false,
          message: '',
          mode: ''
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
              if(!this.interval) {
                this.animate = true
                this.interval = setInterval(() => {
                    if(this.countdown == 0) {
                        this.gameOver()
                        return
                    }
                    this.countdown = this.countdown - 20
                }, 200)
              }
          }
      },
      gameOver() {
          clearInterval(this.interval)
          this.x = ''
          this.y = ''
          this.z = ''
          this.operator = ''
          this.equals = ''
          this.message = 'GAME OVER'
          this.mode = 'GAME_OVER'
      },
      choiceTrue() {
          if(this.mode == 'GAME_OVER')
            return
          if(this.z === (this.x + this.y)) {
              this.score++
              this.initGame(false);
          } else {
              this.gameOver()
          }
      },
      choiceFalse() {
        if(this.mode == 'GAME_OVER')
            return
        if(this.z !== (this.x + this.y)) {
            this.score++;
            this.initGame(false);
        } else {
            this.gameOver()
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
