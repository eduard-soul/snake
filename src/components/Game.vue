<template>
	<div id="game">
		<div id="grid">
			<div class="grid-item" :id="`grid${item-1}`" v-for="item in grid_case" v-bind:key="item">
				<img id="snake-head" v-if="data.head[item-1]" src="../assets/snake_head.svg" alt="snake-head">
				<img id="apple" v-if="data.apple[item-1]" src="../assets/apple.svg" alt="apple">
				<img id="body" v-if="data.body[item-1]" src="../assets/body_straight.svg" alt="body_straight">
			</div>
		</div>
		<h1 id="score">Score : {{data.score}}</h1>
	</div>
	<!-- <input type="text" @keydown.arrow-down="moove_head(data)"> -->
</template>

<script setup>
	import { reactive, ref, onMounted, watch, onUpdated} from "vue";

	let grid_case = ref(50); // The size of the grid is not reactive for now
	let data = reactive({
		head: [],
		body: [],
		apple: [],
		head_rotation: "",
		where_body: [],
		score: 0,
	})

	function add_body(direction) {
		let head = go_to_head(data);

		if (direction == "up") {
			data.body[head] = 1;
		}
		else if (direction == "down") {
			data.body[head] = 2;
		}
		else if (direction == "right") {
			data.body[head] = 3;
		}
		else if (direction == "left") {
			data.body[head] = 4;
		}
		data.where_body.unshift(head);
		return ;
	}

	function turn_head() {
		// console.log(`head rotation = ${data.head_rotation}`);
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

		while (data.head[i] != true) { i++; }
		return (i)
	}

	function init_tab(i, array, which){
		let j = 0;

		while (j < i){
			if (which == "false") {array.push(false);}
			if (which == "minus_one") {array.push(false);}
			j++;
		}
	}

	function check_if_border(nb) {
		// Need to change these values to be reactive to the size of the map
		if (nb % 10 == 0 || nb % 10 == 9 || Math.floor(nb / 10) == 0 || Math.floor(nb / 10) == 4) {
			// console.log(`nb = ${nb / 10} `);
			return (1);
		}
		// console.log(`nb = ${nb / 10} `);
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

	function spawn_apple(data) {
		let rand = Math.floor(Math.random() * 49);
		let head = go_to_head(data);

		while (rand == head || data.body[rand]){
			let rand = Math.floor(Math.random() * 49);
		}
		data.apple[rand] = true;
		// console.log(rand + data.apple[rand]);
	}

	function moove_body(data) {
		let head = go_to_head(data);
		if (data.where_body.length < 1)
		{
			return;
		}

		if (data.head_rotation == "up") {
			data.body[head] = 1;
		}
		else if (data.head_rotation == "down") {
			data.body[head] = 2;
		}
		else if (data.head_rotation == "right") {
			data.body[head] = 3;
		}
		else if (data.head_rotation == "left") {
			data.body[head] = 4;
		}
		let temp1 = data.body[data.where_body[0]];
		let temp2 = data.where_body[0];

		data.body[data.where_body[0]] = 0;
		data.where_body[0] = head;


		let i = 1;
		while (i < data.where_body.length) {
			let where_body = data.where_body[i];
			let direction = data.body[where_body];

				data.body[temp2] = temp1;
				data.where_body[i] = temp2;

			// if (temp2 == 1) {
			// 	data.body[temp2] = temp1;
			// 	data.where_body[i] = temp2;
			// }
			// else if (temp2 == 2) {
			// 	data.body[temp2] = temp1;

			// }
			// else if (temp2 == 3) {				
			// 	data.body[temp2] = temp1;

			// }
			// else if (temp2 == 4) {
			// 	data.body[temp2] = temp1;

			// }
			data.body[where_body] = 0;
			temp1 = direction;
			temp2 = where_body;
			i++;
		}
	}

	function moove_head(data, where) {
		let j = go_to_head(data);

		if ((j % 10 == 0 && where == -1) || (j % 10 == 9 && where == 1) || (j + where >= 50) || (j + where < 0) || data.body[j + where])
		{
			// console.log(j, where);
			alert("GAME OVER");
			return ;
		}
		if (data.apple[j + where] == true) {
			data.apple[j + where] = false;
			data.score++;
			add_body(data.head_rotation);
			data.head[j + where] = true;
			data.head[j] = false;
			spawn_apple(data);
			return ;
		}
		moove_body(data);2
		data.head[j + where] = true;
		data.head[j] = false;
		return ;
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
			// console.log(i);
			if (i > 100) {
				i -= Math.sqrt(Math.sqrt(i)); // Need to modifiy the time, but another formula, or by change the way I do it, because here, the less time there is, the more it do the operation, so less time 
			}
			time_move(i);
		}, i);
	}

	init_tab(50, data.head, "false");
	init_tab(50, data.apple, "false" )
	init_tab(50, data.body, "minus_one")
	spawn_head(data);
	spawn_apple(data);
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
	#game {
		display: flex;
		justify-content: center;
		align-items: center;
		flex-direction: column;
	}
	#score {
		margin-top: 3vh;
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
		justify-content: center;
		align-items: center;
	}
	#snake-head {
		width: 10vh;
		height: auto;
	} 
	#body {
		width: 10vh;
		height: auto;
	}
	#apple {
		width: 7vh;
		height: auto;
	}
</style>