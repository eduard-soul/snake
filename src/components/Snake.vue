<template>
	<Game />
	<div></div>
</template>

<script setup>
	import Game from "./Game.vue"
</script>

<style scoped>

	div {
		position: absolute;
		top: 0px;
		left: 0px;
		width: 100vw;
		height: 100vh;
		z-index: 3;
		/* background-color: violet; */
	}
</style>