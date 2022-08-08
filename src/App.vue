<template>
    <Game/>
</template>

<script>
import Game from './components/Game.vue'

export default {
  name: 'App',
  components: {
    Game
  }
}
</script>

<style lang="scss">
  body {
    padding: 0;
    margin: 0;
    background: url("./img/background.jpg") round;
    font-size: 16px;
  }

.main-app {
  font-family: 'Gluten', cursive;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  margin: 2em auto;
  padding: 1.225em 0;
  width: 640px;
  background-image: linear-gradient( 174.2deg,  rgba(255,244,228,1) 7.1%, rgba(240,246,238,1) 67.4% );
  border-radius: 10px;
}
</style>
