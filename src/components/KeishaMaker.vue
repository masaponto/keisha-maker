<template>
	<div id="app">
		<div class="Title">
			<h1>傾斜つけるくん</h1>
		</div>

		<div class="grid">

			<a>{{ columns.bucho }}</a>
			<input type="number" min="0" v-model="buchoNum" placeholder="0" />
			<a> × </a>
			<a class="num"> {{selectPrice.bucho}} </a>
			<a> ＝ </a>
			<a class="num"> {{posPrice.bucho}} </a>

			<a>{{ columns.kacho }}</a>
			<input type="number" min="0" v-model="kachoNum" placeholder="0" />
			<a> × </a>
			<a class="num"> {{selectPrice.kacho}} </a>
			<a> ＝ </a>
			<a class="num"> {{posPrice.kacho}} </a>

			<a>{{ columns.shusa }}</a>
			<input type="number" min="0" v-model="shusaNum" placeholder="0" />
			<a> × </a>
			<a class="num"> {{selectPrice.shusa}} </a>
			<a> ＝ </a>
			<a class="num"> {{posPrice.shusa}} </a>

			<a>{{ columns.hira }}</a>
			<input type="number" min="0" v-model="hiraNum" placeholder="0" />
			<a> × </a>
			<a class="num">{{ selectPrice.hira }}</a>
			<a> ＝ </a>
			<a class="num"> {{posPrice.hira}} </a>

			<a>{{ columns.extra }}</a>
			<input type="number" min="0" v-model="extraNum" placeholder="0" />
			<a> × </a>
			<a class="num">{{ selectPrice.extra }}</a>
			<a> ＝ </a>
			<a class="num"> {{posPrice.extra}} </a>

			<a>{{ columns.total }}</a>
			<input type="number" min="0" max="1000000" v-model="total" pliceholder="0" />
			<a></a>
			<a></a>
			<a></a>
			<a class="num">{{selectTotal}}</a>

			<a>{{ errorText }}</a>
			<input type="number" min="0" max="5000" v-model="errorVal" pliceholder="0" />
			<a></a>
			<a></a>
			<a></a>
			<a class="num"> {{selectError}}</a>

		</div>
		<div id="calc-button">
			<button large class="calc-btn" @click="calcKeisha()">計算</button>
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
					<tr v-for="keisha in sortedKeishas" @click="showPrice(keisha)">
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
		 for (var i=0; i < 19; i++) {
			 prices[i] = i * 500 + 1000;
		 }

		 const columns = {
			 bucho: '部長',
			 kacho: '課長',
			 shusa:'主査',
			 hira: '社員',
			 extra: 'その他',
			 total: '合計金額'
		 };

		 var sortOrders = {}
		 Object.keys(columns).forEach(function (key) {
			 sortOrders[key] = 1
		 });

		 const selectPrice = {
			 bucho: " ",
			 kacho: " ",
			 shusa: " ",
			 hira: " ",
			 extra: " "
		 }

		 const posPrice = {
			 bucho: " ",
			 kacho: " ",
			 shusa: " ",
			 hira: " ",
			 extra: " "
		 }


		 return { total: '65000',
				  buchoNum: '0',
				  kachoNum: '0',
				  shusaNum: '0',
				  hiraNum:'0',
				  extraNum: '0',
				  prices: prices,
				  columns: columns,
				  keishas: [],
				  sortOrders: sortOrders,
				  sortKey: '',
				  selectPrice: selectPrice,
				  posPrice: posPrice,
				  selectTotal: '',
				  errorText: '許容誤差',
				  errorVal: '500',
				  selectError: '',
		 }
	 },
	 methods: {
		 kCombinations: function(set, k) {
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
				 tailcombs = this.kCombinations(set.slice(i + 1), k - 1);
				 // For each (k-1)-combination we join it with the current
				 // and store it to the set of k-combinations.
				 for (j = 0; j < tailcombs.length; j++) {
					 combs.push(head.concat(tailcombs[j]));
				 }
			 }
			 return combs;
		 },
		 calcTotal: function(combs, nums, kinds) {
			 let total = 0
			 for (var i=0; i < kinds; i++) {
				 total += combs[i] * nums[i].num
			 }
			 return total;
		 },
		 calcKeisha: function(event) {
			 console.log('calc')
			 this.selectPrice = {}
			 this.posPrice = {'bucho': ' ',
							   'kacho': ' ',
							   'shusa': ' ',
							   'hira': ' ',
							   'extra': ' '}

			 this.selectError = ' '
			 this.selectTotal = ' '

			 this.keishas = []

			 const nums = [{name:'extra', num: this.extraNum, rank: 0},
						   {name:'hira', num: this.hiraNum, rank: 1},
						   {name:'shusa', num: this.shusaNum, rank: 2},
						   {name:'kacho', num: this.kachoNum, rank: 3},
						   {name:'bucho', num: this.buchoNum, rank: 4}]

			 const kinds = nums.filter(num => num.num > 0).length
			 let combs = this.kCombinations(this.prices, kinds)
			 combs = combs.map(c => c.sort())
			 let prices = combs.map(c => this.calcTotal(c.sort((a, b) => a - b), nums, kinds))

			 const matchIndeces = prices.map(p => (0 <= (p - this.total)) && ((p - this.total) <= this.errorVal))
			 const matchCombs = combs.filter((c, index) => matchIndeces[index])
			 const matchPrices = prices.filter((p, index) => matchIndeces[index])


			 let selectNums = nums.filter((x) => x.num > 0)
			 selectNums = selectNums.sort(function(a,b){
				 if(a.rank < b.rank) return -1;
				 if(a.rank > b.rank) return 1;
				 return 0;
			 });

			 for (var i = 0; i < matchCombs.length; i++) {
				 let keisha = {total: 0,
							   bucho: 0,
							   kacho: 0,
							   shusa: 0,
							   hira: 0,
							   extra: 0}

				 const c = matchCombs[i]
				 const p = matchPrices[i]


				 keisha['total'] = p

				 let k = 0
				 selectNums.forEach(n => {
					 keisha[n.name] = c[k]
					 k++
				 })

				 this.keishas.push(keisha)
			 }
		 },
		 sortBy: function(key) {
			 this.sortKey = key;
			 this.sortOrders[key] = this.sortOrders[key] * -1;
		 },
		 showPrice: function(keisha) {
			 this.selectPrice = keisha;
			 this.posPrice = {'bucho': keisha.bucho * this.buchoNum,
							   'kacho': keisha.kacho * this.kachoNum,
							   'shusa': keisha.shusa * this.shusaNum,
							   'hira': keisha.hira * this.hiraNum,
							   'extra': keisha.extra * this.extraNum}
			 this.selectTotal = keisha.total
			 this.selectError = keisha.total - this.total
		 }
	 },
	 computed: {
		 //https://www.webopixel.net/javascript/1193.html
		 sortedKeishas: function () {
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

 .num {
	 text-align: right;
 }

 .grid {
	 display: grid;
	 justify-content: center;
	 grid-template-columns: 100px 150px 25px 60px 20px 60px;
	 grid-row-gap: 10px;
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
