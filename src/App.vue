<template>
	<div id="app">
		<div class="centered">
			<label for="cells">Rozmiar (nie mniejszy niż 3)</label>
			<input id="cells" type="number" v-model="cells"/>
		</div>

		<br><br><br><br>

		<div class="cellsArea" v-if="!reset">
			<div v-for="(cell, row) in cellArray" :key="row" class="row">
				<div v-for="(obj, column) in cell" :key="row+column" class="cells">
					<cell-box :row="row" :column="column" :point="value" @clicked="clicked"></cell-box>
				</div>
			</div>
		</div>
	</div>
</template>

<script>
	import CellBox from './components/Cell.vue'

	export default {
		name: 'app',

		components: {
			CellBox
		},

		data() {
			return {
				cells: 3,
				cellArray: [],
				turn: 0,
				value: [],
				reset: false,
			}
		},

		created() {
			this.buildCellArray();
		},

		methods: {
			// Obsługujemy event od dziecka
			clicked: function(data) {
				let result = true;
				let point = '';

				if (this.turn % 2 === 0) {
					point = 'X';
					result = this.assignValue(data.row, data.column, point);
				}
				else {
					point = 'O';
					result = this.assignValue(data.row, data.column, point);
				}

				if (result) {
					this.turn++;

					this.value = [data.row, data.column, point];

					this.checkWinner();
				}
			},

			assignValue: function(row, column, value) {
				if (this.cellArray[row][column] === null) {
					this.cellArray[row][column] = value;

					return true;
				}

				return false;
			},

			buildCellArray: function() {
				this.reset = true;
				this.turn = 0;
				this.cellArray = [];

				for (let i = 0; i < this.cells; i++) {
					this.cellArray[i] = [];

					for (let j = 0; j < this.cells; j++) {
						this.cellArray[i][j] = null;
					}
				}

				this.$nextTick().then(() => {
					this.reset = false;
				});
			},

			checkWinner: function() {
				for (let i = 0; i < this.cells; i++) {
					let rowSum = 0;
					for (let j = 0; j < this.cells; j++) {
						if (this.cellArray[i][j] === 'X') {
							rowSum++;
						}
						else if (this.cellArray[j][i] === 'O') {
							rowSum--;
						}
					}

					if (rowSum === this.cells) {
						this.win('X');
					}
					else if (rowSum === -this.cells) {
						this.win('O');
					}
				}

				for (let i = 0; i < this.cells; i++) {
					let colSum = 0;
					for (let j = 0; j < this.cells; j++) {
						if (this.cellArray[j][i] === 'X') {
							colSum++;
						}
						else if (this.cellArray[j][i] === 'O') {
							colSum--;
						}
					}

					if (colSum === this.cells) {
						this.win('X');
					}
					else if (colSum === -this.cells) {
						this.win('O');
					}
				}

				let diagonal = 0;

				for (let i = 0; i < this.cells; i++) {
					if (this.cellArray[i][i] === 'X') {
						diagonal++;
					}
					else if (this.cellArray[i][i] === 'O') {
						diagonal--;
					}
				}

				if (diagonal === this.cells) {
					this.win('X');
				}
				else if (diagonal === -this.cells) {
					this.win('O');
				}
			},

			win: function(winner) {
				alert(`${winner} wygrywa!`);

				this.buildCellArray();
			}
		},

		watch: {
			cells: function() {
				//Zeby nie byl stringiem
				this.cells = JSON.parse(this.cells);

				if (this.cells < 3) {
					this.cells = 3;
				}

				this.buildCellArray();
			},

			turn: function() {
				if (this.turn === Math.pow(this.cells, 2)) {
					alert('Brak zwycięzcy');
					this.buildCellArray();
				}
			}
		},
	}
</script>

<style>
	.centered {
		text-align: center;
		overflow: hidden;
	}

	.cellsArea {
		justify-content: center;
		margin: 0 auto;
	}

	.cells {
		display: inline-block;
		vertical-align: top;
	}

	.row {
		text-align: center;
	}
</style>
