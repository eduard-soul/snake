<template>
	<Menu v-if="data.menu" v-on:toggleGame="toggledGame"/>
	<Game v-if="data.game"/>
</template>

<script setup>
	import { provide, reactive, ref } from "vue";
	import Game from "./Game.vue";
	import Menu from "./Menu.vue";

	// let diff = ref(1);
	let data = reactive({
		menu: true,
		game: false,
		diff: 1,
	})

	function toggledGame(difficulty) {
		data.diff = difficulty;
		console.log(data.diff);
		data.menu = !data.menu;
		data.game = !data.game;
	}
	provide('data', data);

</script>

<style scoped>

</style>