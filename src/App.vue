<template>
    <b-container class="bv-example-row" fluid>
      <b-row class="justify-content-md-center" align-h="center">
        <Game></Game>
      </b-row>
    </b-container>
</template>

<script>
import Game from './components/Game.vue'

export default {
  name: 'app',
  components: {
    Game
  }
}
</script>

<style>

</style>
