<template>
	<div>
		<div class="Title">
			<h1>Keisha Maker</h1>
		</div>

		<div>
			<div class="total">
				<a>Total</a>
				<input type="number" v-model="total" pliceholder="0" />
			</div>
			<div class="bucho">
				<a>bucho</a>
				<input type="number" v-model="bucho_num" placeholder="0" />
			</div>

			<div class="tanto-bucho">
				<a>tanto-bucho</a>
				<input type="number" v-model="tanto_bucho_num" placeholder="0" />
			</div>

			<div class="kacho">
				<a>kacho</a>
				<input type="number" v-model="kacho_num" placeholder="0" />
			</div>

			<div class="shusa">
				<a>shusa</a>
				<input type="number" v-model="shusa_num" placeholder="0" />
			</div>

			<div class="hira">
				<a>hira</a>
				<input type="number" v-model="hira_num" placeholder="0" />
			</div>

			<div calss="calc-button">
				<button large class="calc-btn" @click="calc()">Run</button>
			</div>
		</div>
		<div>
			<table>
				<thead>
					<tr>
						<th>Index</th>
						<th>Total Price</th>
						<th>Bucho</th>
						<th>TB</th>
						<th>TK</th>
						<th>Shusa</th>
						<th>Hira</th>
					</tr>
				</thead>
				<tbody>
					<!-- <tr v-for="entry in filteredHeroes">

						 </tr> -->
				</tbody>
			</table>
		</div>
	</div>
</template>

<script>
 export default {
	 data() {

		 var prices = new Array(18);
		 for (var i=0; i < 18; i++) {
			 prices[i] = i * 500 + 2000;
		 }

		 return { total: '65000',
				  bucho_num: '0',
				  tanto_bucho_num: '0',
				  kacho_num: '0',
				  shusa_num: '0',
				  hira_num:'0',
				  prices: prices
		 }
	 },
	 methods: {
		 k_combinations: function(set, k) {
			 // https://gist.github.com/axelpale/3118596
			 var i, j, combs, head, tailcombs;

			 // There is no way to take e.g. sets of 5 elements from
			 // a set of 4.
			 if (k > set.length || k <= 0) {
				 return [];
			 }

			 // K-sized set has only one K-sized subset.
			 if (k == set.length) {
				 return [set];
			 }

			 // There is N 1-sized subsets in a N-sized set.
			 if (k == 1) {
				 combs = [];
				 for (i = 0; i < set.length; i++) {
					 combs.push([set[i]]);
				 }
				 return combs;
			 }

			 combs = [];
			 for (i = 0; i < set.length - k + 1; i++) {
				 // head is a list that includes only our current element.
				 head = set.slice(i, i + 1);
				 // We take smaller combinations from the subsequent elements
				 tailcombs = this.k_combinations(set.slice(i + 1), k - 1);
				 // For each (k-1)-combination we join it with the current
				 // and store it to the set of k-combinations.
				 for (j = 0; j < tailcombs.length; j++) {
					 combs.push(head.concat(tailcombs[j]));
				 }
			 }
			 return combs;
		 },
		 calc_total: function(combs, nums, kinds) {
			 let total = 0
			 for (var i=0; i < kinds; i++) {
				 total += combs[i] * nums[i]
			 }
			 return total;
		 },
		 calc: function(event) {
			 const nums = [this.hira_num, this.shusa_num, this.kacho_num, this.tanto_bucho_num, this.bucho_num]
			 const kinds = nums.filter(num => num > 0).length
			 let combs = this.k_combinations(this.prices, kinds)
			 combs = combs.map(c => c.sort())
			 let prices = combs.map(c => this.calc_total(c, nums, kinds))

			 const match_indeces = prices.map(p => (0 <= (p - this.total)) && ((p - this.total) <= 500))
			 const match_combs = combs.filter((c, index) => match_indeces[index])
			 const match_prices = prices.filter((p, index) => match_indeces[index])

			 console.log(match_indeces)
			 console.log(match_combs)
			 console.log(match_prices)
		 }
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
