<template>
	<div id="app">
		<div class="Title">
			<h1>傾斜つけるくん</h1>
		</div>


		<div class="grid">
			<a>{{ totalText }}</a>
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

		<div v-for="(item, index) in items" class="grid">
			<a>{{ item.name }}</a>
			<input type="number" min="0" v-model="item.num" placeholder="0" />
			<a> × </a>
			<a class="num"> {{item.selectPrice}} </a>
			<a> ＝ </a>
			<a class="num"> {{item.posPrice}} </a>
		</div>

		<div id="item-button">
			<button small class="add-btn" @click="addPos()">追加</button>
			<button small class="del-btn" @click="delPos()">削除</button>
		</div>

		<div id="run-button">
			<button large class="run-btn" @click="runKeisha()">計算</button>
		</div>

		<div>
			<table align="center">
				<thead>
					<tr>
						<th v-for="(item, index) in items" @click="sortBy(index)">
							{{ item.name }}
							<span class="arrow" :class="sortOrders[index] > 0 ? 'asc' : 'dsc'"></span>
						</th>
						<th>
							{{totalText}}
						</th>
					</tr>
				</thead>
				<tbody>
					<tr v-for="keisha in sortedKeishas" @click="showPrice(keisha)">
						<td v-for="k in keisha" align="right">
							{{ k }}
						</td>
					</tr>
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

		 let items = [
			 {
				 name: '部長',
				 num: 0,
				 selectPrice: '',
				 posPrice: ''
			 },
			 {
				 name: '課長',
				 num: 0,
				 selectPrice: '',
				 posPrice: ''
			 },
			 {
				 name: '主査',
				 num: 0,
				 selectPrice: '',
				 posPrice: ''
			 },
			 {
				 name: '社員',
				 num: 0,
				 selectPrice: '',
				 posPrice: ''
			 }
		 ]

		 let sortOrders = []
		 items.forEach( x =>
			 sortOrders.push(1)
		 )

		 return { total: '65000',
				  prices: prices,
				  keishas: [],
				  sortOrders: sortOrders,
				  sortIndex: '',
				  totalText: '合計',
				  selectTotal: '',
				  errorText: '許容誤差',
				  errorVal: '0',
	              selectError: '',
	              items: items
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
		 calcTotal: function(combs, kinds) {
			 let total = 0
			 for (var i=0; i < kinds; i++) {
				 total += combs[i] * this.items[i].num
			 }
			 return total;
		 },
		 runKeisha: function(event) {
			 this.calcKeisha()
		 },
		 calcKeisha: function() {
			 const kinds = this.items.length

			 for (var i = 0; i < kinds; i++) {
				 this.items[i].selectPrice = ' '
				 this.items[i].posPrice = ' '
			 }

			 this.selectError = ' '
			 this.selectTotal = ' '
			 this.keishas = []


			 let combs = this.kCombinations(this.prices, kinds)
			 combs = combs.map(c => c.sort())
			 let prices = combs.map(c => this.calcTotal(c.sort((a, b) => b - a), kinds))

			 const matchIndeces = prices.map(p => (0 <= (p - this.total)) && ((p - this.total) <= this.errorVal))
			 const matchCombs = combs.filter((c, index) => matchIndeces[index])
			 const matchPrices = prices.filter((p, index) => matchIndeces[index])

			 for (var i = 0; i < matchCombs.length; i++) {
				 let keisha = []

				 const c = matchCombs[i]
				 const p = matchPrices[i]
				 let k = 0

				 for (var j = 0; j < kinds; j++) {
					 if (this.items[j].num == 0) {
						 keisha.push(0)
						 continue
					 }
					 keisha.push(c[k])
					 k++
				 }
				 keisha.push(p)
				 this.keishas.push(keisha)
			 }
		 },
		 sortBy: function(index) {
			 this.sortIndex = index;
			 this.sortOrders[index] = this.sortOrders[index] * -1
			 this.sortOrders = this.sortOrders.concat()
		 },
		 showPrice: function(keisha) {

			 for (var i = 0; i < this.items.length; i++) {
				 this.items[i].selectPrice = keisha[i]
				 this.items[i].posPrice = keisha[i] * this.items[i].num
			 }

			 this.selectTotal = keisha[keisha.length -1]
			 this.selectError = keisha[keisha.length -1] - this.total
		 },
		 addPos: function(event) {
			 if (this.items.length < 10) {
				 let pos = {
					 name: '追加-' + (this.items.length - 3),
					 num: 0,
					 selectPrice: '',
					 posPrice: ''
				 }
				 this.items.push(pos)
				 this.calcKeisha()
			 }
		 },
		 delPos: function(event) {
			 if (this.items.length > 4) {
				 this.items.pop()
				 this.calcKeisha()
			 }
		 }
	 },
	 computed: {
		 //https://www.webopixel.net/javascript/1193.html
		 sortedKeishas: function () {
			 let data = this.keishas
			 let sortIndex = this.sortIndex
			 let order = this.sortOrders[sortIndex] || 1

			 data = data.slice().sort(function(a, b){
				 a = a[sortIndex]
				 b = b[sortIndex]
				 return (a === b ? 0 : a > b ? 1 : -1) * order
			 })

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
	 margin-bottom: 8px;
 }

 .run-btn {
	 margin-top: 10px;
	 margin-bottom: 25px;
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

 .run-btn:hover{
	 background-color: #2d825b;
 }

 .run-btn:active{
	 position: relative;
	 top: 1px;
	 left: 1px;
	 box-shadow: none;
 }

 .add-btn {
	 margin-top: 2px;
	 margin-bottom: 10px;
	 margin-right: 5px;
	 width: 60px;
	 height: 25px;
     border: none;
     background: #35495e;
     color: #fff;
     font-weight: 500;
     border-radius: 4px;
     cursor: pointer;
     text-align: center;
     cursor: pointer;
     font-weight: bold;
     box-shadow: 1px 1px 3px 0px rgba(0, 0, 0, 0.2);
 }

 .add-btn:hover{
	 background-color: #35495e;
 }

 .add-btn:active{
	 position: relative;
	 top: 1px;
	 left: 1px;
	 box-shadow: none;
 }

 .del-btn {
	 margin-top: 2px;
	 margin-bottom: 10px;
	 margin-left: 5px;
	 width: 60px;
	 height: 25px;
     border: none;
     background: #5f6864;
     color: #fff;
     font-weight: 500;
     border-radius: 4px;
     cursor: pointer;
     text-align: center;
     cursor: pointer;
     font-weight: bold;
     box-shadow: 1px 1px 3px 0px rgba(0, 0, 0, 0.2);
 }

 .del-btn:hover{
	 background-color: #3e4442;
 }

 .del-btn:active{
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
