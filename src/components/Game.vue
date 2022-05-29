<template>
	<div id="game">
		<div id="game-over" v-if="data.end">
			<h1 id="what-name">What is your name ?</h1>
			<div id="send-player">	
				<input type="text" id="player-name" v-model="data.player_name" required minlength="3" maxlength="4" placeholder="NAME" @keyup.enter="add_score_to_history">
				<button type="button" @click="add_score_to_history">REPLAY</button>
			</div>
			<h1 id="choose-difficulty">Next game Difficulty ?</h1>
			<div id="difficulty-level">
				<button v-on:click="change_difficulty(0)" id="easy" class="difficulty-btn">EASY</button>
				<button v-on:click="change_difficulty(1)" id="normal" class="difficulty-btn">NORMAL</button>
				<button v-on:click="change_difficulty(2)" id="hard" class="difficulty-btn">HARD</button>
			</div>
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
			<h1>&nbsp;</h1>
			<h1 class="score" id="score">Score : {{data.score}}</h1>
			<div class="apple-score">
				<img src="../assets/apple.svg" alt="apple">
				<h1>&nbsp;: {{data.apple_score}}</h1>
			</div>
		</div>
	</div>
</template>

<script setup>
	import { reactive, ref, onMounted, onUpdated, inject} from "vue";

	let grid_case = ref(50);
	let data = reactive({
		head: [],
		head_rotation: "",
		body: [],
		apple: [],
		where_body: [],
		apple_score: 0,
		score: 0,
		player_name: "",
		end: false,
		diff: 1,
		bonus: 100,
	})
	data.diff = inject('data').diff;

	function init_tab(i, array, which){
		let j = 0;

		while (j < i){
			if (which == "false") {array.push(false);}
			if (which == "zero") {array.push(0);}
			j++;
		}
	}
	function change_size() {
		let grid = document.getElementById("grid");
		let snake_head = document.getElementById("snake-head");
		let grid_item = document.getElementsByClassName("grid-item");

		if (data.diff == 2) {
			grid.style.gridTemplateColumns = "repeat(20, 5vh)";
			grid.style.gridTemplateRows = "repeat(10, 5vh)";
			snake_head.style.width = "5vh";
			for (let i = 0; i < grid_item.length; i++) {
				grid_item[i].style.width = "5.1vh";
				grid_item[i].style.height = "5.1vh";
				// console.log(i);
			}
		}
		else {
			grid.style.gridTemplateColumns = "repeat(10, 10vh)";
			grid.style.gridTemplateRows = "repeat(5, 10vh)";
			snake_head.style.width = "10vh";
			for (let i = 0; i < grid_item.length; i++) {
				grid_item[i].style.width = "10.1vh";
				grid_item[i].style.height = "10.1vh";
			}
		}
	}

	function change_difficulty(difficulty) {
		let button = document.getElementsByClassName('difficulty-btn');
		for (let i = 0; i < button.length; i++) {
			button[i].classList.remove('difficulty-active');
		}
		button[difficulty].classList.add('difficulty-active');
		data.diff = difficulty;
	}

	function init_game(which) {
		if (data.diff == 2) { grid_case.value = 200; }
		else {grid_case.value = 50}

		if (which == "reset") {
			data.head.length = 0;
			data.body.length = 0;
			data.apple.length = 0;
			data.where_body.length = 0;
			data.score = 0;
			data.apple_score = 0;
		}
		init_tab(grid_case.value, data.head, "false");
		init_tab(grid_case.value, data.body, "zero")
		init_tab(grid_case.value, data.apple, "false" )
		spawn_head(data);
		spawn_apple(data);
		if (which != "reset") {
			event_listener_html();
		}
		if (which == "reset") {
			let time;

			if (data.diff == 0) {time = 800}
			else if (data.diff == 1) {time = 1000}
			else {time = 700}
			data.end = false;
			console.log(data.end);
			time_move(time);
		}
	}

	function go_to_head(data) {
		let i = 0;

		while (data.head[i] != true) { i++;}
		return (i)
	}

	function check_if_border(nb) {
		// Need to change these values to be reactive to the size of the map
		let size_line;
		if (data.diff == 2) {
			size_line = 20; 
		} else {
			size_line = 10;
		}

		if (nb % size_line == 0 || nb % size_line == size_line-1 || Math.floor(nb / size_line) == 0 || Math.floor(nb / size_line) == size_line/2-1) {
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
		change_size();
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

	function apply_rotation_body_part(body, angle, align) {
		body.style.transform = `rotate(${angle})`;
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
					apply_rotation_body_part(body_straight, "90deg");
				}
				else if (data.body[data.where_body[i]] == 2) {
					apply_rotation_body_part(body_straight, "270deg");
				}
				else if (data.body[data.where_body[i]] == 3) {
					apply_rotation_body_part(body_straight, "180deg");
				}
				else if (data.body[data.where_body[i]] == 4) {
					apply_rotation_body_part(body_straight, "0deg");
				}
			}
			else {
					var body_turn = document.getElementById(`body-t${data.where_body[i]}`);

					if ((data.body[data.where_body[i]] == 2 && data.body[data.where_body[i + 1]] == 3)
					|| data.body[data.where_body[i]] == 4 && data.body[data.where_body[i + 1]] == 1) {
						apply_rotation_body_part(body_turn, "90deg");
					}
					else if ((data.body[data.where_body[i]] == 2 && data.body[data.where_body[i + 1]] == 4)
					|| data.body[data.where_body[i]] == 3 && data.body[data.where_body[i + 1]] == 1) {
						apply_rotation_body_part(body_turn, "0deg");
					}
					else if ((data.body[data.where_body[i]] == 1 && data.body[data.where_body[i + 1]] == 4)
					|| data.body[data.where_body[i]] == 3 && data.body[data.where_body[i + 1]] == 2) {
						apply_rotation_body_part(body_turn, "270deg");
					}
					else if ((data.body[data.where_body[i]] == 1 && data.body[data.where_body[i + 1]] == 3)
					|| data.body[data.where_body[i]] == 4 && data.body[data.where_body[i + 1]] == 2) {
						apply_rotation_body_part(body_turn, "180deg");
					}
			}
		}
		if (data.where_body.length) {
			var tail = document.getElementById(`tail${data.where_body[i]}`);
			var grid_item = grid_items[data.where_body[i]];

			if (data.body[data.where_body[i]] == 1 ) {
				apply_rotation_body_part(tail, "270deg");
			}
			else if (data.body[data.where_body[i]] == 2) {
				apply_rotation_body_part(tail, "90deg");
			}
			else if (data.body[data.where_body[i]] == 3) {
				apply_rotation_body_part(tail, "0deg");
			}
			else if (data.body[data.where_body[i]] == 4) {
				apply_rotation_body_part(tail, "180deg");
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
		let i;
		if (data.diff == 2) { i = 199}
		else {i = 49}

		let rand = Math.floor(Math.random() * i);
		while (check_if_border(rand) == 1)
		{
			rand = Math.floor(Math.random() * i);
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
		let i;
		if (data.diff == 2) { i = 199}
		else {i = 49}

		let rand = Math.floor(Math.random() * i);
		let head = go_to_head(data);

		while (rand == head || data.body[rand] > 0){
			rand = Math.floor(Math.random() * i);
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
		let size_line;
		let size_total;

		if (data.diff == 2) {
			size_line = 20;
			size_total = 200;
		} else {
			size_line = 10;
			size_total = 50;
		}


		if ((j % size_line == 0 && where == -1) || (j % size_line == size_line - 1 && where == 1) || (j + where >= size_total) || (j + where < 0) || data.body[j + where])
		{
			data.end = true;
			return (-1);
		}
		if (data.apple[j + where] == true) {
			data.apple[j + where] = false;
			data.apple_score++;
			data.score += (10 * 1) + (10 * data.diff * 2) + data.bonus;
			data.bonus = 100;
			add_body(data.head_rotation);
			data.head[j + where] = true;
			data.head[j] = false;
			spawn_apple(data);
		}
		else {
			if (data.bonus != 0){
				data.bonus -= 5;
			}
			moove_body(data);
			data.head[j + where] = true;
			data.head[j] = false;
		}
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
				if (data.diff == 2) {
					move = -20;
				} else {
					move = -10;
				}
			}
			else if (data.head_rotation == "down") {
				if (data.diff == 2) {
					move = 20;
				} else {
					move = 10;
				}
			}
			else if (data.head_rotation == "right") {
				move = 1;
			}
			else {
				move = -1;
			}
			if (moove_head(data, move) == -1) {
				console.log(data.diff);
				return (-1);
			}
			if (data.diff == 0) {
				i = 800;
			}
			else if (i > 200 && data.diff == 1) {
				i -= Math.sqrt(Math.sqrt(Math.sqrt(i)));
			}
			else if (i > 100 && data.diff == 2) {
				i -= Math.sqrt(Math.sqrt(i)); 	
			}
			time_move(i);
		}, i);
	}

	init_game();
	onMounted(() => {
		let time;

		if (data.diff == 0) {time = 800}
		else if (data.diff == 1) {time = 1000}
		else {time = 700}
		turn_head();
		change_size();
		time_move(time);
	})

	onUpdated(() => {
		if (data.end != true) {
			turn_head();
			change_size();
			rotate_body_parts();
		}
		if (data.end == true) {
			change_difficulty(data.diff);
		}
})
</script>

<style scoped>
	* {
		padding: 0px;
		margin: 0px;
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
		width: 100vh;
		height: 5vh;
		overflow: hidden;
		margin-top: 3vh;
		/* background-color: blue; */
		display: flex;
		justify-content: space-between;
		align-items: center;
	}
	.score {
		margin-top: 1vh;
	}
	.apple-score {
		display: flex;
		justify-content: center;
		align-items: flex-end;
		/* background-color: green; */
	}
	.apple-score img {
		height: 5vh;
		/* background-color: red; */
	}
	.apple-score h1 {
		height: 4vh;
		/* background-color: aqua; */
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
		width: 10.1vh;
		height: 10.1vh;
		border: 0.05vh solid black;
		background-color: #96EB88;
		display: flex;
		justify-content: center;
		align-items: center;
	}
	#snake-head {
		width: 100%;
		height: auto;
	} 
	.body-straight {
		width: 100%;
		height: auto;
	}
	.body-turn {
		width: 100%;
		height: auto;
	}
	.body-tail {
		width: 100%;
		height: auto;
	}
	#apple {
		width: 70%;
	}
	#game-over {
		display: flex;
		flex-direction: column;
		align-items: center;
		top: 31vh;
		height: 27vh;
		width: 35vw;
		background-color: #2c3e50;
		position: absolute;
		z-index: 10;
		border-radius: 2vh;
		border: 0.5vh solid black;
		/* opacity: 0.8; */
	}
	#what-name, #choose-difficulty {
		margin-top: 2.5vh;
		margin-bottom: 1vh;
		color: white;		
	}
	#send-player {
		display: flex;
		justify-content: space-between;
		width: 70%;
	}
	#player-name {
		border: none;
		width: 20vh;
		height: 4vh;
		border-radius: 1vh;
		text-transform: uppercase;
	}
	#send-player button {
		height: 4vh;
		width: 7vh;
		border-radius: 1vh;
		border: none;
	}

	#difficulty-level {
		width: 65%;
		display: flex;
		justify-content: space-between;
		margin: 0vh;
	}

	.difficulty-btn{
		font-weight: bold;
		width: 8vh;
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
</style>