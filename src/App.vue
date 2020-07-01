<template>
  <div id="app">
    <clock/>
    <h2>Latest session is one minute behind <span id="caution">sorted by pps</span></h2>
    <p v-if="session_name != ''">Session name: <span id="session-id">{{session_name}}</span></p>
    <p v-else>Loading......</p>
    <search @set-search="set_search" @get-session="get_session"
            @get-search="set_data" @get-dst="set_dst"/>
    <tables @session-change="get_session" v-if="!is_search"/>
    <table-search v-else v-bind:search_data="search_data" v-bind:search_hdr="search_hdr" v-bind:search_data_dst="search_data_dst"/>
    <!-- <div id="caution"><h4>sorted by pps</h4></div> -->
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
      search_data_dst:[],
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
        tmp[2] == 0 ? tmp[2] = "IPv4" : tmp[2] = "IPv6"
        tmp[3] == 0 ? tmp[3] = "src" :tmp[3] = "dst"
        tmp[4] = parseFloat(tmp[4]).toFixed(2)
        tmp[5] = parseFloat(tmp[5]).toFixed(2)
        tmp = tmp.filter(i => i != "src")
        this.search_data.push(tmp)
      }
      this.search_hdr = x[1].filter(i=>i != "end point")
      console.log(y)
    },
    set_dst:function(s){
      let i;
      for(i in s){
        let tmp = s[i].split(",")
        tmp[2] == 0 ? tmp[2] = "IPv4" : tmp[2] = "IPv6"
        tmp[3] == 0 ? tmp[3] = "src" :tmp[3] = "dst"
        tmp[4] = parseFloat(tmp[4]).toFixed(2)
        tmp[5] = parseFloat(tmp[5]).toFixed(2)
        tmp = tmp.filter(i => i != "dst")
        this.search_data_dst.push(tmp)
      }
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
