<template>
	<div id="game">
		<div id="grid">
			<div class="grid-item" :id="`grid${item-1}`" v-for="item in grid_case" v-bind:key="item">
				<img id="snake-head" v-if="data.head[item-1]" src="../assets/snake_head.svg" alt="">
			</div>
		</div>
	</div>
	<!-- <input type="text" @keydown.arrow-down="moove_head(data)"> -->
</template>

<script setup>
	import { reactive, ref } from "@vue/reactivity";

	let grid_case = ref(50); // The size of the grid is not reactive for now
	let data = reactive({
		head: [],
		head_rotation: "up", 
	})

	function create_tab(i, array){
		let j = 0;

		while (j < i){
			array.push(false);
			j++;
		}
	}

	function spawn_head(data) {
		let rand = Math.floor(Math.random() * 49);
		data.head[rand] = true;
	}

	function moove_head(data, where) {
		let j = 0;

		while (j < data.head.length) {
			if (data.head[j] == true)
			{
				if ((j % 10 == 0 && where == -1) || (j % 10 == 9 && where == 1) || (j + where >= 50) || (j + where < 0))
				{
					// console.log(j, where);
					alert("GAME OVER");
					return ;
				}
				data.head[j] = false;
				data.head[j + where] = true;
				return ;
			}
			j++;
			// console.log(j);
		}
		// console.log("LIO");
	}
	// There is a problem, in go in to GAME OVER when the code is update

	function sendArrowKeys(event) {
		if (event.key == "ArrowUp") {
			moove_head(data, -10);
			data.head_rotation = "up";
		}
		if (event.key == "ArrowDown") {
			moove_head(data, 10);
			data.head_rotation = "down";
		}
		if (event.key == "ArrowRight") {
			moove_head(data, 1);
			data.head_rotation = "right";
		}
		if (event.key == "ArrowLeft") {
			moove_head(data, -1);
			data.head_rotation = "left";
		}
	}

	function event_listener_html() {
		document.getElementsByTagName("html")[0].addEventListener('keydown', sendArrowKeys);
	}


	create_tab(50, data.head);
	spawn_head(data);
	event_listener_html();


</script>

<style>
	* {
		padding: 0px;
		margin: 0px;
	}

	#grid {
		width: 100.25vh;
		height: 50.25vh;
		display: grid;
		grid-template-columns: repeat(10, 10vh);
		grid-template-rows: repeat(5, 10vh);
		border: 0.5vh solid black;
	}
	.grid-item {
		width: 10vh;
		height: 10vh;
		border: 0.25vh solid black;
		background-color: grey;
		display: flex;
		justify-content: flex-start;
		align-items: center;
	}
	#snake-head {
		width: 10vh;
		height: auto;
	} 
</style>