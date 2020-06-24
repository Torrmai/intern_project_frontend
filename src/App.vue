<template>
  <div id="app">
    <clock/>
    <h2>Latest session is one minute behind</h2>
    <p>Display below is the latest one or  you can search other session</p>
    <search/>
    <tables/>
    <div id="caution"><h4>sorted by total number of packet in one minute interval</h4></div>
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
  padding-top:0px ;
}
#caution{
    padding-top: 10px;
    color: rgb(119, 28, 0);
    /* border: 3px solid red; */
}
</style>
