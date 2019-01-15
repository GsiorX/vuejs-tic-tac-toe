<template>
	<div class="cell" @click="clicked">
		{{ value }}
	</div>
</template>

<script>
	export default {
		name: 'cell-box',

		props: {
			row: {
				type: Number,
				required: true,
			},

			column: {
				type: Number,
				required: true,
			},

			point: {
				type: Array,
				required: false,
			}
		},

		data() {
			return {
				value: '',
			}
		},

		methods: {
			clicked: function() {
				// Wysyłamy event do parenta przy kliknięciu na pole
				this.$emit('clicked', {
					row: this.row,
					column: this.column,
				});
			}
		},

		watch: {
			point: function() {
				let point = this.point;

				if (point[0] !== undefined && point[1] !== undefined && point[2] !== undefined) {
					if (point[0] === this.row && point[1] === this.column) {
						this.value = point[2];
					}
				}
			}
		}
	}
</script>


<style scoped>
	.cell {
		height: 100px;
		width: 100px;
		border: 1px solid black;
		line-height: 90px;
		text-align: center;
		font-size: 50px;
	}
</style>
