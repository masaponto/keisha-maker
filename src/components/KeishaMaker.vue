<template>
	<div>
		<div class="Title">
			<h1>Keisha Maker</h1>
		</div>

		<div>
			<div class="total">
				<a>Total</a>
				<input type="number" v-model="total" pliceholder="0">
			</div>
			<div class="bucho">
				<a>bucho</a>
				<input type="number" v-model="bucho_num" placeholder="0">
			</div>

			<div class="tanto-bucho">
				<a>tanto-bucho</a>
				<input type="number" v-model="tanto_bucho_num" placeholder="0">
			</div>

			<div class="kacho">
				<a>kacho</a>
				<input type="number" v-model="kacho_num" placeholder="0">
			</div>

			<div class="shusa">
				<a>shusa</a>
				<input type="number" v-model="shusa_num" placeholder="0">
			</div>

			<div class="hira">
				<a>hira</a>
				<input type="number" v-model="hira_num" placeholder="0">
			</div>

			<div calss="calc-button">
				<button large class="calc-btn" @click="calc()">Run</button>
			</div>
		</div>

		<!-- <table>
			 <thead>
			 <tr>
			 <th>Index</th>
			 <th>Total Price</th>
			 <th>Bucho</th>
			 <th>TB</th>
			 <th>TK</th>
			 <th>Shusa</th>
			 </tr>
			 </thead>
			 <tbody>
			 <tr v-for="entry in filteredHeroes">

			 </tr>
			 </tbody>
			 </table> -->
	</div>
</template>

<script>
 export default {
	 data() {

		 var prices = new Array(18);
		 for (var i=0; i < 18; i++) {
			 prices[i] = i * 500 + 2000;
		 }

		 return { bucho_num: '0',
				  tanto_bucho_num: '0',
				  kacho_num: '0',
				  shusa_num: '0',
				  hira_num:'0',
				  prices: prices
		 }
	 },
	 methods: {
		 calc: function(event) {
			 const nums = [this.hira_num, this.shusa_num, this.kacho_num, this.tanto_bucho_num, this.bucho_num]

			 console.log(this.prices)
			 console.log(nums);
		 },

	 }
 }

</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->

<style scoped>
h3 {
	margin: 40px 0 0;
}
ul {
	list-style-type: none;
	padding: 0;
}
li {
	display: inline-block;
	margin: 0 10px;
}
a {
	color: #42b983;
}
</style>
