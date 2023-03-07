<script>
	import { onMount } from 'svelte';
	let ctx,
		star,
		ship,
		ships = [];
	let fps = 120;
	let numShips = 150;
	let speed = .001
	onMount(() => {
		const canvas = document.getElementById('canvas');
		ctx = canvas.getContext('2d');

		// Define the star
		star = {
			x: canvas.width / 2,
			y: canvas.height / 2,
			radius: 5,
			color: '#000FFF'
		};

		generateShips(numShips);
		animateShip();
	});

	function generateShips(numShips) {
		// Define the ship
		for (let i = 0; i < numShips; i++) {
			let xpos, ypos, rand;
			rand = Math.random() > 0.5;
			// xpos = canvas.width / 2 * (rand ? 1.5 : -1.5);
			// ypos = canvas.width / 2 * (rand ? 1.5  : -1.5);
			xpos = canvas.width / 2;
			ypos = canvas.width / 2;
			let color = 360 - (80 / numShips) * i;
			ship = {
				x: xpos,
				y: ypos,
				radius: canvas.width / 300,
				color: color,
				// speed:  Math.random() / 50,
				speed: speed,
				angle: (2 * Math.PI * i) / numShips
				// angle: i * xpos + ypos
			};
			ships.push(ship);
			ship.color;
		}
	}

	// Draw the star
	function drawStar(star) {
		ctx.beginPath();
		ctx.arc(star.x, star.y, star.radius, 0, Math.PI * 2, false);
		ctx.fillStyle = star.color;
		// ctx.fillStyle = star.color += 0.1;
		ctx.fill();
		ctx.closePath();
	}

	// Draw the ship
	function drawShip(ship) {
		ctx.beginPath();
		ctx.arc(ship.x, ship.y, ship.radius, 0, Math.PI * 2, false);
		ctx.fillStyle = `hsla(${ship.color}, 100%, 50%, 1)`;
		ctx.fill();
		ctx.closePath();
	}

	function redrawShip1(i) {
		ship = ships[i];
		let randomValue = getRandomValueInRange(0, numShips);
		// Update the ship's angle
		// ship.speed = ship.speed * Math.random();
		ship.angle += ship.speed / numShips;
		// update ship speed
		// Calculate the new x and y coordinates - using tangent gives a very interesting result
		ship.x = star.x + Math.cos(ship.angle + i / randomValue) * 50;
		ship.y = star.y + Math.sin(ship.angle - i / randomValue) * 50;
		drawShip(ship);
	}
	function redrawShip2(i) {
		ship = ships[i];
		let randomValue = getRandomValueInRange(0, numShips);
		// Update the ship's angle
		ship.angle += ship.speed / 15;
		// ship.speed = .001 - (randomValue / 3000);
		ship.speed = 0.01;
		// Calculate the new x and y coordinates - using tangent gives a very interesting result
		ship.x = star.x + Math.cos(ship.angle + i) * 50;
		ship.y = star.y + Math.sin((ship.angle - i) * ships.length) * 5;
		drawShip(ship);
	}

	function loopShips1() {
		for (let i = 0; i < ships.length; i++) {
			ship = ships[i];
			let randomValue = getRandomValueInRange(0, numShips);
			// Update the ship's angle
			// ship.speed = ship.speed * Math.random();
			ship.angle += ship.speed;
			// update ship speed
			// Calculate the new x and y coordinates - using tangent gives a very interesting result
			ship.x = star.x + Math.cos(ship.angle) * 50;
			ship.y = star.y + Math.sin(ship.angle) * 50;
			drawShip(ship);
		}
	}
	function loopShips2() {
		for (let i = 0; i < ships.length; i++) {
			ship = ships[i];
			let randomValue = getRandomValueInRange(0, numShips);
			// Update the ship's angle
			ship.angle += ship.speed / 15;
			// ship.speed = .001 - (randomValue / 3000);
			ship.speed = 0.001;
			// Calculate the new x and y coordinates - using tangent gives a very interesting result
			ship.x = star.x + Math.cos(ship.angle - i) * 50;
			ship.y = star.y + Math.sin((ship.angle - i) * ships.length) * 5;
			drawShip(ship);
		}
	}

	function getRandomValueInRange(min, max) {
		return Math.random() * (max - min) + min;
	}

	// Animate the ship's orbit
	let counter = 0;
	function animateShip() {
		ctx.clearRect(0, 0, canvas.width, canvas.height);

		// counter++
		let rand = Math.random();
		let posVar = 1;
		// if(counter > 60){
		// 	counter = 0
		// 	rand = Math.random()
		// 	console.log(`🚀 ~ file: +page.svelte:75 ~ animateShip ~ rand:`, rand)
		// 	posVar = rand
		// }

		// let posVar =  rand > 0.5 ? rand * 2 : rand * -2

		for (let i = 0; i < ships.length; i++) {
			ship = ships[i];
			rand = Math.random() > 0.5;
			// Update the ship's angle
			// ship.angle = ship.angle + (rand ? (-ship.speed * i) / 15 : (ship.speed * i) / 15);
			ship.angle = ship.speed + ship.angle  * i / 25;
			ship.angle += ship.speed / ship.angle;
			//
			// Calculate the new x and y coordinates - using tangent gives a very interesting result
			// console.log(`🚀 ~ file: +page.svelte:75 ~ animateShip ~ ship.angle:`, ship.angle)

			// calculate new x and y coordinates using trigonometry, with location variation
			// ship.x = star.x + Math.cos(ship.angle + ships.length / i) * 50; // very interesting assymetric pattern
			// ship.x = star.x + Math.cos(ship.angle + ships.length * i) * 50; // ditto, unexpected
			// ship.x = star.x + Math.cos((ship.angle * ships.length) / i) * 50; // super interesting whirlwind!
			ship.x = star.x + Math.tan(ship.angle / i * ships.length) * 50; // similar/same as above
			// ship.x = star.x + Math.cos(ship.angle / ships.length * i) * 50; // slow, orderly vortex
			// ship.x = star.x + Math.cos(ship.angle / i * ships.length) * 50;

			// ship.x = star.x + Math.cos(ship.angle + i ) * 50;
			ship.y = star.y + Math.tan((ship.angle / i) * ships.length / i) * 25;

			// ship.y = star.y + Math.sin(ship.angle ) * 50;
			// ship.x = star.x + i *.1 + Math.cos(ship.angle) * 50;
			// ship.y = star.y + i * .1 + Math.sin(ship.angle) * 50;
			// ship.x = star.x + posVar + Math.cos(ship.angle) * 50;
			// ship.y = star.y + posVar + Math.sin(ship.angle) * 50;
			// ship.color += 0.1;
			drawStar(star);
			redrawShip1(i);
			redrawShip2(i);
			// drawShip(ship);
		}
		// loopShips2()
		setTimeout(() => {
			requestAnimationFrame(animateShip);
		}, 1000 / fps);
	}
</script>

<canvas id="canvas" />

<style>
	canvas {
		width: 100%;
		height: 100%;
	}
</style>
