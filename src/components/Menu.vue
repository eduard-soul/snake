<template>
	<div id="menu-wrapper">
		<div id="menu">
			<!-- <h1>MENU</h1> -->
			<img src="../assets/menu_logo.svg" alt="menu_logo">
			<div id="difficulty-level">
				<button v-on:click="change_difficulty(0)" id="easy" class="difficulty-btn">EASY</button>
				<button v-on:click="change_difficulty(1)" id="normal" class="difficulty-btn difficulty-active">NORMAL</button>
				<button v-on:click="change_difficulty(2)" id="hard" class="difficulty-btn">HARD</button>
			</div>
			<div id="play">
				<button @click="$emit('toggleGame', data.difficulty)" id="play-btn">PLAY</button>
			</div>
		</div>
	</div>
</template>

<script setup>
import { reactive } from "@vue/reactivity";


	let data = reactive({
		difficulty: 1,
	})

	function change_difficulty(difficulty) {
		let button = document.getElementsByClassName('difficulty-btn');
		for (let i = 0; i < button.length; i++) {
			button[i].classList.remove('difficulty-active');
		}
		button[difficulty].classList.add('difficulty-active');
		data.difficulty = difficulty;
	}
</script>

<style scoped>
	#menu-wrapper {
		margin-top: 5vh;
		display: flex;
		justify-content: center;
	}

	#menu {
		width: 100vh;
		height: 50vh;
		background-color: darkslategray;
		border: 1vh solid black;
		border-radius: 2vh;
	}

	#menu img {
		width: 90vh;
	}

	#difficulty-level {
		width: 65%;
		display: flex;
		justify-content: space-between;
		margin: 5vh auto auto auto;
	}

	.difficulty-btn{
		font-weight: bold;
		width: 12vh;
		height: 4vh;
		border-radius: 1vh;
		border: none;
	}

	#easy {
		background-color: rgb(255, 255, 119);
	}
	#easy:hover {
		background-color: rgb(235, 235, 113);
	}

	#normal {
		background-color: rgb(255, 199, 95);
	}
	#normal:hover {
		background-color: rgb(231, 182, 77);
	}

	#hard {
		background-color: rgb(255, 158, 119);
	}
	#hard:hover {
		background-color: rgb(231, 144, 109);
	}

	.difficulty-active {
		outline: 0.6vh solid rgba(131, 255, 162, 0.596);
	}

	#play-btn {
		width: 20vh;
		height: 5vh;
		margin-top: 7vh;
		border-radius: 3vh;
		background-color: rgba(131, 255, 162, 0.596) ;
		color: white;
		font-size: 2.5vh;
		font-weight: bold;
		border: none;
	}
	#play-btn:hover {
		background-color: rgba(131, 255, 162, 0.699) ;
	}
	#play-btn:active {
		background-color: rgba(37, 201, 78, 0.308) ;
	}
</style>