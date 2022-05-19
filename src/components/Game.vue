<template>
	<div id="game">
		<div id="grid">
			<div class="grid-item" :id="`grid${item-1}`" v-for="item in grid_case" v-bind:key="item">
				<img id="snake-head" v-if="data.head[item-1]" src="../assets/snake_head.svg" alt="snake-head">
			</div>
		</div>
	</div>
	<!-- <input type="text" @keydown.arrow-down="moove_head(data)"> -->
</template>

<script setup>
	import { reactive, ref, onMounted, watch, onUpdated} from "vue";

	let grid_case = ref(50); // The size of the grid is not reactive for now
	let data = reactive({
		head: [],
		head_rotation: "", 
	})

	function turn_head() {
		console.log(`head rotation = ${data.head_rotation}`);
		// let temp = document.getElementById("snake-head").style.transform;
		// console.log(`dId = ${temp}`);
		if (data.head_rotation == "up") {
			document.getElementById("snake-head").style.transform="rotate(-90deg)";
		}
		if (data.head_rotation == "down") {
			document.getElementById("snake-head").style.transform="rotate(90deg)";
		}
		if (data.head_rotation == "left") {
			document.getElementById("snake-head").style.transform="rotate(180deg)";
		}
		if (data.head_rotation == "right") {
			document.getElementById("snake-head").style.transform="rotate(0deg)";
		}
	}

	function go_to_head(data) {
		let i = 0;

		while (data.head[i] != true)
		{
			i++;
		}

		return (i)
	}

	function create_tab(i, array){
		let j = 0;

		while (j < i){
			array.push(false);
			j++;
		}
	}

	function check_if_border(nb) {
		// Need to change these values to be reactive to the size of the map
		if (nb % 10 == 0 || nb % 10 == 9 || Math.floor(nb / 10) == 0 || Math.floor(nb / 10) == 4) {
			console.log(`nb = ${nb / 10} `);
			return (1);
		}
		console.log(`nb = ${nb / 10} `);
		return (0);
	}

	function spawn_head(data) {
		let rand = Math.floor(Math.random() * 49);
		while (check_if_border(rand) == 1)
		{
			rand = Math.floor(Math.random() * 49);
		}
		data.head[rand] = true;

		rand = Math.floor(Math.random() * 4)
		if (rand == 0){
			data.head_rotation = "up";
		}
		else if (rand == 1){
			data.head_rotation = "down";
		}
		else if (rand == 2){
			data.head_rotation = "right";
		}
		else {
			data.head_rotation = "left";
		}
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
		}
	}
	// There is a problem, in go in to GAME OVER when the code is update

	function sendArrowKeys(event) {
		if (event.key == "ArrowUp") {
			data.head_rotation = "up";
		}
		else if (event.key == "ArrowDown") {
			data.head_rotation = "down";
		}
		else if (event.key == "ArrowRight") {
			data.head_rotation = "right";
		}
		else if (event.key == "ArrowLeft") {
			data.head_rotation = "left";
		}
		else {
			return ;
		}
	}

	function event_listener_html() {
		document.getElementsByTagName("html")[0].addEventListener('keydown', sendArrowKeys);
	}

	function time_move(i) {
		setTimeout(() => {
			if (data.head_rotation == "up") {moove_head(data, -10);}
			else if (data.head_rotation == "down") {moove_head(data, 10);}
			else if (data.head_rotation == "right") {moove_head(data, 1);}
			else {moove_head(data, -1);}
			console.log(i);
			if (i > 100) {
				i -= Math.sqrt(Math.sqrt(i)); // Need to modifiy the time, but another formula, or by change the way I do it, because here, the less time there is, the more it do the operation, so less time 
			}
			time_move(i);
		}, i);
	}

	create_tab(50, data.head);
	spawn_head(data);
	event_listener_html();

	onMounted(() => {
		turn_head();
		time_move(1000);
	})

	onUpdated(() => {
	  turn_head();
})
</script>

<style scoped>
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