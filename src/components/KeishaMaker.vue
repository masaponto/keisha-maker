<template>
	<div>
		<div class="Title">
			<h1>傾斜つけるくん(β)</h1>
		</div>

		<div class="grid">
			<a>{{ columns.total }}</a>
			<input type="number" min="0" max="1000000" v-model="total" pliceholder="0" />

			<a>{{ columns.bucho }}</a>
			<input type="number" min="0" v-model="bucho_num" placeholder="0" />

			<a class="pos">{{ columns.tbucho }}</a>
			<input type="number" min="0" v-model="tanto_bucho_num" placeholder="0" />

			<a>{{ columns.kacho }}</a>
			<input type="number" min="0" v-model="kacho_num" placeholder="0" />

			<a>{{ columns.shusa }}</a>
			<input type="number" min="0" v-model="shusa_num" placeholder="0" />

			<a>{{ columns.hira }}</a>
			<input type="number" min="0" v-model="hira_num" placeholder="0" />
		</div>
		<div id="calc-button">
			<button large class="calc-btn" @click="calc()">計算</button>
		</div>
		<div>
			<table align="center">
				<thead>
					<tr>
						<th v-for="(value, key) in columns" @click="sortBy(key)">
							{{ value }}
							<span class="arrow" :class="sortOrders[key] > 0 ? 'asc' : 'dsc'"></span>
						</th>
					</tr>
				</thead>
				<tbody>
					<tr v-for="keisha in sortedKeishas">
						<td v-for="(value, key) in columns" align="right">
							{{ keisha[key] }}
						</td>
					</tr>
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

		 const columns = {
			 bucho: '部長',
			 tbucho: '担当部長',
			 kacho: '課長',
			 shusa:'主査',
			 hira: '社員',
			 total: '金額'
		 };

		 var sortOrders = {}
		 Object.keys(columns).forEach(function (key) {
			 sortOrders[key] = 1
		 });

		 return { total: '65000',
				  bucho_num: '0',
				  tanto_bucho_num: '0',
				  kacho_num: '0',
				  shusa_num: '0',
				  hira_num:'0',
				  prices: prices,
				  columns: columns,
				  keishas: [],
				  sortOrders: sortOrders,
				  sortKey: ''
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
				 total += combs[i] * nums[i].num
			 }
			 return total;
		 },
		 calc: function(event) {

			 this.keishas = []

			 const nums = [{name:'hira', num: this.hira_num},
						   {name:'shusa', num: this.shusa_num},
						   {name:'kacho', num: this.kacho_num},
						   {name:'tbucho', num: this.tanto_bucho_num},
						   {name:'bucho', num: this.bucho_num}]

			 const kinds = nums.filter(num => num.num > 0).length
			 console.log(kinds)
			 let combs = this.k_combinations(this.prices, kinds)
			 combs = combs.map(c => c.sort())
			 let prices = combs.map(c => this.calc_total(c, nums, kinds))

			 const match_indeces = prices.map(p => (0 <= (p - this.total)) && ((p - this.total) <= 500))
			 const match_combs = combs.filter((c, index) => match_indeces[index])
			 const match_prices = prices.filter((p, index) => match_indeces[index])

			 for (var i = 0; i < match_combs.length; i++) {
				 let keisha = {total: 0,
							   bucho: 0,
							   tbucho: 0,
							   kacho: 0,
							   shusa: 0,
							   hira: 0}

				 const c = match_combs[i]
				 const p = match_prices[i]

				 keisha['total'] = p

				 var k = 0
				 nums.forEach(num => {
					 if (num.num !=0) {
						 keisha[num.name] = c[k]
						 k++
					 }
				 })

				 this.keishas.push(keisha)
			 }
		 },
		 sortBy: function(key) {
			 console.log('sort by')
			 console.log(key)
			 this.sortKey = key;
			 this.sortOrders[key] = this.sortOrders[key] * -1;
		 }
	 },
	 computed: {
		 //https://www.webopixel.net/javascript/1193.html
		 sortedKeishas: function () {
			 console.log('sortedKeishas')
			 let data = this.keishas;
			 let sortKey = this.sortKey;
			 let order = this.sortOrders[sortKey] || 1;

			 if (sortKey) {
				 data = data.slice().sort(function(a, b){
					 a = a[sortKey];
					 b = b[sortKey];
					 return (a === b ? 0 : a > b ? 1 : -1) * order;
				 });
			 }

			 return data
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

 input {
	 text-align: right;
	 border-radius: 4px;
	 border: 1px solid #ccc;
 }

 input:focus{
	 border-color: #42b983;
 }


 .grid {
	 display: grid;
	 justify-content: center;
	 grid-template-columns: 100px 150px;
 }

 .calc-btn {
	 margin-top: 20px;
	 margin-bottom: 20px;
	 width: 250px;
	 height: 30px;
     border: none;
     background: #42b983;
     color: #fff;
     font-weight: 500;
     border-radius: 4px;
     cursor: pointer;
     text-align: center;
     cursor: pointer;
     font-weight: bold;
     box-shadow: 1px 1px 3px 0px rgba(0, 0, 0, 0.2);
 }

 .calc-btn:hover{
	 background-color: #2d825b;
 }

 .calc-btn:active{
	 position: relative;
	 top: 1px;
	 left: 1px;
	 box-shadow: none;
 }

 table {
	 border: 2px solid #42b983;
	 border-radius: 3px;
	 background-color: #fff;
 }

 th {
	 background-color: #42b983;
	 color: rgba(255,255,255,0.66);
	 cursor: pointer;
	 -webkit-user-select: none;
	 -moz-user-select: none;
	 -ms-user-select: none;
	 user-select: none;
 }

 td {
	 background-color: #f9f9f9;
 }

 th, td {
	 min-width: 30px;
	 padding: 10px 20px;
 }

 th.active {
	 color: #fff;
 }

 th.active .arrow {
	 opacity: 1;
 }

 .arrow {
	 display: inline-block;
	 vertical-align: middle;
	 width: 0;
	 height: 0;
	 margin-left: 5px;
	 opacity: 0.66;
 }

 .arrow.asc {
	 border-left: 4px solid transparent;
	 border-right: 4px solid transparent;
	 border-bottom: 4px solid #fff;
 }

 .arrow.dsc {
	 border-left: 4px solid transparent;
	 border-right: 4px solid transparent;
	 border-top: 4px solid #fff;
 }

</style>
