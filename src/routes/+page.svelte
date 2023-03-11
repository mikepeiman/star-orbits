<script>
	import { onMount } from 'svelte';
	let ctx,
		star,
		ship,
		ships = [];

		let modifiers = {
			orbitModX: 50,
			orbitModY: 50,
		}
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

		// number of ships
		let numShips = 45
		generateShips(numShips);
		animateShip();
	});

	function generateShips(numShips) {
		// Define the ship 
		for (let i = 0; i < numShips; i++) {
			let xpos, ypos, rand
			rand = Math.random() > 0.5;
			xpos = canvas.width / 2 
			ypos = canvas.width / 2 
			let color = 360 - (80 / numShips * i);
			ship = {
				x: xpos,
				y: ypos,
				radius: canvas.width / 300,
				color: color,
				speed: 0.01,
				angle: 2 * Math.PI * i / numShips
			};
			ships.push(ship);
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


	// Animate the ship's orbit
	let counter = 0
	function animateShip() {
		ctx.clearRect(0, 0, canvas.width, canvas.height);
		for (let i = 0; i < ships.length; i++) {
			ship = ships[i];
			ship.angle += ship.speed;
			// Calculate the new x and y coordinates - using tangent gives a very interesting result
			// calculate new x and y coordinates to give ships a circular orbit
			ship.x = star.x + Math.cos(ship.angle) * modifiers.orbitModX;
			ship.y = star.y + Math.sin(ship.angle) * modifiers.orbitModY;
			ship.color += .1
			drawStar(star);
			drawShip(ship);
		}
		requestAnimationFrame(animateShip);
	}
</script>

<canvas id="canvas" />

<style>
	canvas {
		width: 100%;
		height: 100%;
	}
</style>
