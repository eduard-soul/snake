<template>
  <!-- <HelloWorld msg="Hello Vue 3 + Vite" /> -->
  <img id="snake-logo" alt="snake logo" src="./assets/snake_logo.svg" />
  <Snake></Snake>
</template>

<script setup>
// import HelloWorld from './components/HelloWorld.vue'
import Snake from './components/Snake.vue'


fetch('../scoreboard.json').then(res => res.json()).then(data => console.log(data.scoreboard[0].player_name));
// .then(data => console.log(data));

</script>

<style>
* {
  padding: 0px;
  margin: 0px;
}

#snake-logo {
  width: 60vh;
  height: auto;
  margin-top: 2vh;
}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  text-align: center;
  color: #2c3e50;
}

/* #game {
  width: 100vw;
  height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
} */
</style>
