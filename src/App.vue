<template>
  <div id="app">
      <KeishaMaker/>
  </div>
</template>

<script>
//import HelloWorld from './components/HelloWorld.vue'
import KeishaMaker from './components/KeishaMaker.vue'

export default {
  name: 'app',
  components: {
      //HelloWorld
	  KeishaMaker
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
