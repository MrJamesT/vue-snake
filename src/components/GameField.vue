<template>
	<div class="gameField">
		<v-layout
			column
			v-touch="{
				left: () => move({ keyCode: 37 }),
				right: () => move({ keyCode: 39 }),
				up: () => move({ keyCode: 38 }),
				down: () => move({ keyCode: 40 })
			}"
		>
			<div v-for="y in gameHeight" :key="y" class="squareRow">
				<div
					v-for="x in gameWidth"
					:key="x"
					class="square"
					:class="{ 'scorePoint': pointX == x && pointY == y && !isSnake(x, y), 'snake': isSnake(x, y), 'grass': !isSnake(x, y) && (pointX != x || pointY != y) }"
				></div>
			</div>
		</v-layout>
	</div>
</template>

<script>
export default {
	props: ['gameRunning'],
	data() {
		return {
			direction: ['R'],
			score: 0,
			gameWidth: 25,
			gameHeight: 25,
			x: new Array(),
			y: new Array(),
			pointX: 0,
			pointY: 0,
			gameInterval: undefined
		}
	},
	watch: {
		gameRunning: function(nV) {
			if (this.gameInterval == undefined && nV) {
				this.start()
			} else if (this.gameInterval != undefined && !nV) {
				this.restart()
			}
		}
	},
	methods: {
		rnd(min, max) {
			return Math.floor(Math.random() * (max - min + 1)) + min
		},
		findAllIndexes(arr, what) {
			return arr
				.map(function(c, i) {
					if (c == what) return i
				})
				.filter(function(v) {
					return v >= 0
				})
		},
		isSnake(posX, posY) {
			let xIndexes = this.findAllIndexes(this.x, posX)
			let yIndexes = this.findAllIndexes(this.y, posY)
			let combined = (typeof xIndexes !== 'undefined' && xIndexes.length == 0) || (typeof yIndexes !== 'undefined' && yIndexes.length == 0) ? [] : xIndexes.filter(el => yIndexes.includes(el))
			return combined.length > 0 ? true : false
		},
		move(e) {
			if (e.keyCode == 37 && this.direction[0] != 'R' && this.direction[0] != 'L') this.direction.push('L')
			else if (e.keyCode == 38 && this.direction[0] != 'D' && this.direction[0] != 'U') this.direction.push('U')
			else if (e.keyCode == 39 && this.direction[0] != 'L' && this.direction[0] != 'R') this.direction.push('R')
			else if (e.keyCode == 40 && this.direction[0] != 'U' && this.direction[0] != 'D') this.direction.push('D')
		},
		start() {
			// Initialize Snake in the middle
			for (let i = 3; i > 0; i--) {
				this.x.push(Math.ceil(this.gameWidth / 2) - i)
				this.y.push(Math.ceil(this.gameHeight / 2))
			}

			// Create a random point to collect
			this.pointY = this.rnd(1, this.gameHeight)
			this.pointX = this.rnd(1, this.gameWidth)
			// Check if the point isn't in the middle, otherwise the snake would eat it immiadetely
			if (this.pointY == Math.ceil(this.gameHeight / 2)) this.pointY += 1

			// Add keydown listeners for movement
			document.addEventListener('keydown', this.move)

			// Start movement of Snake
			this.gameInterval = setInterval(
				function() {
					this.gameLoop()
				}.bind(this),
				75
			)
		},
		restart() {
			clearInterval(this.gameInterval)
			this.direction = ['R']
			this.score = 0
			this.x = []
			this.y = []
			this.gameInterval = undefined
			this.$emit('game-finished')
		},
		gameLoop() {
			// Remove the executed move from the queue of moves
			if (this.direction.length > 1) this.direction.shift()

			if (this.direction[0] == 'R') {
				this.x.push(this.x[this.x.length - 1] + 1)
				this.y.push(this.y[this.y.length - 1])
			} else if (this.direction[0] == 'U') {
				this.x.push(this.x[this.x.length - 1])
				this.y.push(this.y[this.y.length - 1] - 1)
			} else if (this.direction[0] == 'L') {
				this.x.push(this.x[this.x.length - 1] - 1)
				this.y.push(this.y[this.y.length - 1])
			} else if (this.direction[0] == 'D') {
				this.x.push(this.x[this.x.length - 1])
				this.y.push(this.y[this.y.length - 1] + 1)
			}

			// Check if the snake hasn't hit an edge
			if (Math.min(...this.x) < 1 || Math.min(...this.y) < 1 || Math.max(...this.x) > this.gameWidth || Math.max(...this.y) > this.gameHeight) {
				this.$emit('alert-message', {
					heading: 'Game Over!',
					text: "You've lost! But no worries, you can try again, your score was " + this.score
				})
				this.restart()
			}

			// Check if the snake has eaten the collectible point
			if (this.x.includes(this.pointX) && this.y.includes(this.pointY) && this.x.indexOf(this.pointX) == this.y.indexOf(this.pointY)) {
				// Add a new block to snake
				this.x.unshift(this.pointX)
				this.y.unshift(this.pointY)

				// Increase player score
				this.score++

				// Create a new random edible block
				this.pointY = this.rnd(1, this.gameHeight)
				this.pointX = this.rnd(1, this.gameWidth)

				// Make sure the new point isn't in the snake
				while (this.x.includes(this.pointX)) this.pointX = this.rnd(1, this.gameWidth)
				while (this.y.includes(this.pointY)) this.pointY = this.rnd(1, this.gameHeight)
			}

			// Remove the snakes tail so it won't get longer each loop
			this.x.shift()
			this.y.shift()

			// Check against crashes into the snake itself
			for (let a = 0; a < this.x.length - 1; a++) {
				if (this.x[a] == this.x[this.x.length - 1] && this.y[a] == this.y[this.y.length - 1]) {
					this.$emit('alert-message', {
						heading: 'Game Over!',
						text: "You've lost! But no worries, you can try again, your score was " + this.score
					})
					this.restart()
				}
			}
		}
	}
}
</script>

<style lang="scss" scoped>
.gameField {
	border: 15px solid #578a34;
	border-radius: 15px;
}

.squareRow {
	display: flex;
	flex-direction: row;
	height: 12px;
}

.square {
	width: 12px;
	height: 12px;
	margin: 0;
}

@media (min-width: 500px) {
	.square {
		width: 15px;
		height: 15px;
	}

	.squareRow {
		height: 15px;
	}
}

@media (min-width: 700px) {
	.square {
		width: 20px;
		height: 20px;
	}

	.squareRow {
		height: 20px;
	}
}

@media (min-width: 1000px) {
	.square {
		width: 25px;
		height: 25px;
	}

	.squareRow {
		height: 25px;
	}
}

.squareRow:nth-child(even) {
	.grass:nth-child(even) {
		background-color: #a2d149;
	}

	.grass:nth-child(odd) {
		background-color: #aad751;
	}
}

.squareRow:nth-child(odd) {
	.grass:nth-child(odd) {
		background-color: #a2d149;
	}

	.grass:nth-child(even) {
		background-color: #aad751;
	}
}

.snake {
	background-color: #4573e8;
}

.scorePoint {
	background-color: #e8481d;
}
</style>