<template>
  <div id="app">
    <clock/>
    <h2>Latest session is one minute behind</h2>
    <p v-if="session_name != ''">Session name: <span id="session-id">{{session_name}}</span></p>
    <p v-else>Loading......</p>
    <search @set-search="set_search" @get-session="get_session"
            @get-search="set_data"/>
    <tables @session-change="get_session" v-if="!is_search"/>
    <table-search v-else v-bind:search_data="search_data" v-bind:search_hdr="search_hdr"/>
    <div id="caution"><h4>sorted by total number of packet in one minute interval</h4></div>
  </div>
</template>

<script>
import clock from './components/clock.vue'
import search from "./components/search.vue";
import tables from "./components/tables.vue";
import tableSearch from "./components/tableSearch.vue"

export default {
  name: 'App',
  components: {
    clock,
    search,
    tables,
    tableSearch
  },
  data: function(){
    return{
      session_name:"",
      is_search:false,
      search_data:[],
      search_hdr:[]
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
      this.is_search = s
    },
    set_data:function(...arg){
      const [x,y] = arg
      let i;
      for(i in x[0]){
        let tmp = x[0][i].split(",")
        tmp[2] == 0 ? tmp[2] = "ipV4" : tmp[2] = "ipV6"
        tmp[3] == 0 ? tmp[3] = "src" :tmp[3] = "dst"
        tmp[4] = parseFloat(tmp[4]).toFixed(0)
        tmp[5] = parseFloat(tmp[5]).toFixed(0)
        tmp[6] = parseFloat(tmp[6]).toFixed(2)
        tmp[7] = parseFloat(tmp[7]).toFixed(2)
        this.search_data.push(tmp)
      }
      this.search_hdr = x[1].split(",")
      console.log(y)
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
