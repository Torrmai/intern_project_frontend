<template>
  <div id="app">
    <clock/>
    <h2>Latest session is one minute behind</h2>
    <p v-if="session_name != ''">Session name: <span id="session-id">{{session_name}}</span></p>
    <p v-else>Loading......</p>
    <search @set-search="set_search" @get-session="get_session"/>
    <tables @session-change="get_session" v-if="!is_search"/>
    <h1 v-else>Test</h1>
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
      session_name:"",
      is_search:false
    }
  },
  mounted : function() {
  },
  beforeDestroy(){
  },
  methods:{
    get_session:function(params) {
      this.session_name = params
    },
    set_search:function(s) {
      console.log("searching....")
      this.is_search = s
    }
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
#caution,#session-id{
    padding-top: 10px;
    color: rgb(255, 2, 2);
    /* border: 3px solid red; */
}
#session-id{
  font-weight: 900;
}
</style>
