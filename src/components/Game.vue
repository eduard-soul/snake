<template>
	<div id="game">
		<!-- <div id="title"><h1>Snake Game</h1></div> -->
		<div id="game-over" v-if="data.end">
			<input type="text" id="player-name" v-model="data.player_name" required minlength="3" maxlength="4" placeholder="NAME" @keyup.enter="add_score_to_history">
			<input type="button" @click="add_score_to_history">
		</div>
		<div id="grid">
			<div class="grid-item" :id="`grid${item-1}`" v-for="item in grid_case" v-bind:key="item">
				<img id="snake-head" v-if="data.head[item-1]" src="../assets/snake_head.svg" alt="snake-head">
				<img id="apple" v-if="data.apple[item-1]" src="../assets/apple.svg" alt="apple">
				<img :id="`body-s${item-1}`" class="body-straight" v-if="which_body_part(item-1) == 1" src="../assets/body_straight.svg" alt="body_straight">
				<img :id="`body-t${item-1}`" class="body-turn" v-if="which_body_part(item-1) == 2" src="../assets/body_turn.svg" alt="body_turn">
				<img :id="`tail${item-1}`" class="body-tail" v-if="which_body_part(item-1) == 3" src="../assets/tail.svg" alt="tail">
			</div>
		</div>
		<div id="score-wrapper">
			<h1 class="score" id="score">Score : {{data.score[0]}}</h1>
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
		body: [],
		apple: [],
		where_body: [],
		score: [0],
		player_name: "",
		end: false,
	})

	function init_tab(i, array, which){
		let j = 0;

		while (j < i){
			if (which == "false") {array.push(false);}
			if (which == "zero") {array.push(0);}
			j++;
		}
	}

	function init_game(which) {
		if (which == "reset") {
			data.head.length = 0;
			data.body.length = 0;
			data.apple.length = 0;
			data.where_body.length = 0;
			data.score.unshift(0);
		}
		init_tab(50, data.head, "false");
		init_tab(50, data.body, "zero")
		init_tab(50, data.apple, "false" )
		spawn_head(data);
		spawn_apple(data);
		if (which != "reset") {
			event_listener_html();
		}
		if (which == "reset") {
			time_move(1000);
			data.end = false;
		}
	}

	function go_to_head(data) {
		let i = 0;

		while (data.head[i] != true) { i++;}
		return (i)
	}

	function check_if_border(nb) {
		// Need to change these values to be reactive to the size of the map
		if (nb % 10 == 0 || nb % 10 == 9 || Math.floor(nb / 10) == 0 || Math.floor(nb / 10) == 4) {
			return (1);
		}
		return (0);
	}

	function add_score_to_history() {
		if (data.player_name.length < 3){
			alert("PLAYER NAME NEED TO BE BETWEEN 3 OR 4 CHARACTERS")
			return (-1);
		}
		console.log(`${data.player_name}'s score : ${data.score[0]}`);
		init_game("reset");
	}

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
		let head = document.getElementById("snake-head");

		if (data.head_rotation == "up") {
			head.style.transform="rotate(-90deg)";
		}
		if (data.head_rotation == "down") {
			head.style.transform="rotate(90deg)";
		}
		if (data.head_rotation == "left") {
			head.style.transform="rotate(180deg)";
		}
		if (data.head_rotation == "right") {
			head.style.transform="rotate(0deg)";
		}
	}

	function apply_rotation_body_part(body, grid, angle,justify, align) {
		body.style.transform = `rotate(${angle})`;
		if (justify != "none") {
			grid.style.justifyContent = justify;
		}
		if (align != "none") {
			grid.style.alignItems = align;
		}
	}

	function rotate_body_parts() {
		var grid_items = document.getElementsByClassName("grid-item");
		let i;

		for (i = 0; i < data.body.length; i++) {
			grid_items[i].style.justifyContent = "center";
			grid_items[i].style.alignItems = "center";
		}
		for (i = 0; i < data.where_body.length - 1; i++) {
			var grid_item = grid_items[data.where_body[i]];
			if (data.body[data.where_body[i]] == data.body[data.where_body[i + 1]] || i == data.where_body.length - 1) {
				var body_straight = document.getElementById(`body-s${data.where_body[i]}`);

				if (data.body[data.where_body[i]] == 1 ) {
					apply_rotation_body_part(body_straight, grid_item, "90deg", "none", "none");
				}
				else if (data.body[data.where_body[i]] == 2) {
					apply_rotation_body_part(body_straight, grid_item, "270deg", "none", "none");
				}
				else if (data.body[data.where_body[i]] == 3) {
					apply_rotation_body_part(body_straight, grid_item, "180deg", "none", "none");
				}
				else if (data.body[data.where_body[i]] == 4) {
					apply_rotation_body_part(body_straight, grid_item, "0deg", "none", "none");
				}
			}
			else {
					var body_turn = document.getElementById(`body-t${data.where_body[i]}`);

					if ((data.body[data.where_body[i]] == 2 && data.body[data.where_body[i + 1]] == 3)
					|| data.body[data.where_body[i]] == 4 && data.body[data.where_body[i + 1]] == 1) {
						// apply_rotation_body_part(body_turn, grid_item, "90deg", "flex-start", "flex-end");
						apply_rotation_body_part(body_turn, grid_item, "90deg", "none", "none");
					}
					else if ((data.body[data.where_body[i]] == 2 && data.body[data.where_body[i + 1]] == 4)
					|| data.body[data.where_body[i]] == 3 && data.body[data.where_body[i + 1]] == 1) {
						// apply_rotation_body_part(body_turn, grid_item, "0deg", "flex-end", "flex-end");
						apply_rotation_body_part(body_turn, grid_item, "0deg", "none", "none");
					}
					else if ((data.body[data.where_body[i]] == 1 && data.body[data.where_body[i + 1]] == 4)
					|| data.body[data.where_body[i]] == 3 && data.body[data.where_body[i + 1]] == 2) {
						// apply_rotation_body_part(body_turn, grid_item, "270deg", "flex-end", "flex-start");
						apply_rotation_body_part(body_turn, grid_item, "270deg", "none", "none");
					}
					else if ((data.body[data.where_body[i]] == 1 && data.body[data.where_body[i + 1]] == 3)
					|| data.body[data.where_body[i]] == 4 && data.body[data.where_body[i + 1]] == 2) {
						// apply_rotation_body_part(body_turn, grid_item, "180deg", "flex-start", "flex-start");
						apply_rotation_body_part(body_turn, grid_item, "180deg", "none", "none");
					}
			}
		}
		if (data.where_body.length) {
			var tail = document.getElementById(`tail${data.where_body[i]}`);
			var grid_item = grid_items[data.where_body[i]];

			if (data.body[data.where_body[i]] == 1 ) {
				// apply_rotation_body_part(tail, grid_item, "270deg", "none", "flex-start");
				apply_rotation_body_part(tail, grid_item, "270deg", "none", "none");
			}
			else if (data.body[data.where_body[i]] == 2) {
				// apply_rotation_body_part(tail, grid_item, "90deg", "none", "flex-end");
				apply_rotation_body_part(tail, grid_item, "90deg", "none", "none");
			}
			else if (data.body[data.where_body[i]] == 3) {
				// apply_rotation_body_part(tail, grid_item, "0deg", "flex-end", "none");
				apply_rotation_body_part(tail, grid_item, "0deg", "none", "none");
			}
			else if (data.body[data.where_body[i]] == 4) {
				// apply_rotation_body_part(tail, grid_item, "180deg", "flex-start", "none");
				apply_rotation_body_part(tail, grid_item, "180deg", "none", "none");
			}
		}

	}

	function which_body_part(index) {
		let i = 0;

		if (!data.body[index])
		{
			return (0);
		}
		while (i < data.where_body.length && data.where_body[i] != index)
			i++;
		if (i == data.where_body.length - 1) {
			return (3);
		}
		else if (i == data.where_body.length - 1) {
			return (1);
		}
		else {
			if (data.body[data.where_body[i]] == data.body[data.where_body[i + 1]]) {
				return (1)
			}
			else {
				return (2);
			}
		}
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

		while (rand == head || data.body[rand] > 0){
			rand = Math.floor(Math.random() * 49);
		}
		data.apple[rand] = true;
	}

	function moove_body(data) {
		let head = go_to_head(data);
		let i = 1;
		let temp1; 
		let temp2;
		
		if (data.where_body.length < 1) {
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

		temp1= data.body[data.where_body[0]];
		temp2 = data.where_body[0];
		data.body[data.where_body[0]] = 0;
		data.where_body[0] = head;

		while (i < data.where_body.length) {
			let where_body = data.where_body[i];
			let direction = data.body[where_body];
			data.body[temp2] = temp1;
			data.where_body[i] = temp2;
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
			data.end = true;
			// alert("GAME OVER");
			// init_game("reset");
			return (-1);
		}
		if (data.apple[j + where] == true) {
			data.apple[j + where] = false;
			data.score[0]++;
			add_body(data.head_rotation);
			data.head[j + where] = true;
			data.head[j] = false;
			spawn_apple(data);
		}
		else {
			moove_body(data);
			data.head[j + where] = true;
			data.head[j] = false;
		}
		// rotate_body_parts();
	}

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
			let move;

			if (data.head_rotation == "up") {
				move = -10;
			}
			else if (data.head_rotation == "down") {
				move = 10;
			}
			else if (data.head_rotation == "right") {
				move = 1;
			}
			else {
				move = -1;
			}
			if (moove_head(data, move) == -1) {
				// time_move(1000);
				return (-1);
			}
			if (i > 100) {
				i -= Math.sqrt(Math.sqrt(i));
			}
			time_move(i);
		}, i);
	}

	init_game();
	onMounted(() => {
		turn_head();
		time_move(1000);
	})

	onUpdated(() => {
		if (data.end != true) {
			turn_head();
			rotate_body_parts();
		}
})
</script>

<style scoped>
	* {
		padding: 0px;
		margin: 0px;
	}
	#title {
		width: 102.5vh;
		height: 6vh;
		margin-bottom: auto;
		margin-top: auto;
		border-radius: 1vh;	
		display: flex;
		justify-content: center;
		align-items: center;
		background-color: white;
		border: 0.4vh solid black;
		color: rgb(28, 31, 28);
	}
	#game {
		margin-top: 5vh;
		display: flex;
		justify-content: center;
		align-items: center;
		flex-direction: column;
	}
	#score-wrapper {
		position: relative;
		height: 19vh;
		overflow: hidden;
		margin-top: 3vh;
	}
	.score {
		margin-top: 1vh;
	}
	#score0 {
		opacity: 100%;
	}
	#score1 {
		opacity: 70%;
	}
	#score2 {
		opacity: 30%;
	}
	#score3 {
		opacity: 10%;
	}
	#grid {
		width: 100vh;
		height: 50vh;
		display: grid;
		grid-template-columns: repeat(10, 10vh);
		grid-template-rows: repeat(5, 10vh);
		border-radius: 3vh;
		overflow: hidden;
		border: 1vh solid black;
	}
	.grid-item {
		width: 10vh;
		height: 10vh;
		border: 0.05vh solid black;
		background-color: #96EB88;
		display: flex;
		justify-content: center;
		align-items: center;
	}
	#snake-head {
		width: 10.1vh;
		height: auto;
	} 
	.body-straight {
		width: 10.1vh;
		height: auto;
	}
	.body-turn {
		width: 10.1vh;
		height: auto;
	}
	.body-tail {
		width: 10.1vh;
		height: auto;
	}
	#apple {
		width: 70%;
	}
	#game-over {
		top: 31vh;
		height: 32vh;
		width: 35vw;
		background-color: white;
		position: absolute;
		z-index: 10;
		border-radius: 2vh;
		/* opacity: 0.8; */
	}
	#player-name {
		text-transform: uppercase;
	}
</style>