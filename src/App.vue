<template>
  <div id="app">
    <clock/>
    <h2>Latest session is one minute behind</h2>
    <p>Display below is the latest one or  you can search other session</p>
    <search/>
    <tables/>
  </div>
</template>

<script>
import clock from './components/clock.vue'
import search from "./components/search.vue";
import tables from "./components/tables.vue";

export default {
  name: 'App',
  components: {
    clock,
    search,
    tables
  },
  data: function(){
    return{
    }
  },
  mounted : function() {
  },
  beforeDestroy(){
  },
  methods:{
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 20px;
}
</style>
