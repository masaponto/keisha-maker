<template>
	<div>
		<div class="Title">
			<h1>Keisha Maker</h1>
		</div>

		<div class="bucho">
			<a>bucho</a>
			<input v-model="bucho_num" placeholder="0">
			<input v-model="bucho_price" placeholder="0">
		</div>

		<div class="tanto-bucho">
			<a>tanto-bucho</a>
			<input v-model="tanto_bucho_num" placeholder="0">
			<input v-model="tanto_bucho_price" placeholder="0">
		</div>

		<div class="kacho">
			<a>kacho</a>
			<input v-model="kacho_num" placeholder="0">
			<input v-model="kacho_price" placeholder="0">
		</div>

		<div class="shusa">
			<a>shusa</a>
			<input v-model="shusa_num" placeholder="0">
			<input v-model="shusa_price" placeholder="0">
		</div>

		<div class="hira">
			<a>hira</a>
			<input v-model="hira_num" placeholder="0">
			<input v-model="hira_price" placeholder="0">
		</div>

	</div>
</template>

<script>

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
