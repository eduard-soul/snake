<template>
  <!-- <HelloWorld msg="Hello Vue 3 + Vite" /> -->
  <!-- <img alt="Vue logo" src="./assets/logo.png" /> -->
  <Snake></Snake>
</template>

<script setup>
// import HelloWorld from './components/HelloWorld.vue'
import Snake from './components/Snake.vue'


</script>

<style>
* {
  padding: 0px;
  margin: 0px;
}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

#game {
  width: 90vw;
  height: 90vh;
  display: flex;
  justify-content: center;
  align-items: center;
}
</style>
