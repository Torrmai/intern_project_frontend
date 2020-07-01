<template>
    <div id="table_container">
        <div class="table_area">
            <h3>ip src statistic</h3>
            <table id="main_tb">
                <tr>
                    <th v-for="hdr in search_hdr" :key="hdr">{{hdr}}</th>
                </tr>
                <tr v-for="n in search_data" :key="n">
                    <td v-for="j in n" :key="j">{{j}}</td>
                </tr>
            </table>            
        </div>
        <div class="table_area">
            <h3>ip dst statistic</h3>
            <table id="main_tb">
                <tr>
                    <th v-for="hdr in search_hdr" :key="hdr">{{hdr}}</th>
                </tr>
                <tr v-for="n in search_data_dst" :key="n">
                    <td v-for="j in n" :key="j">{{j}}</td>
                </tr>
            </table>            
        </div>
        <!-- <h1 v-else>Loading....</h1> -->
    </div>
</template>
<script>
export default {
    name:"tableSearch",
    data(){
        return{
        }
    },
    props:{
        search_hdr:Array,
        search_data:Array,
        search_data_dst:Array
    },
    mounted: function(){
    },
    updated(){
    },
    methods:{
    }
}
</script>
<style scoped>
table, th, td {
  border: 1px solid black;
  border-collapse: collapse;
}
th,td{
    padding-left: 15px;
    padding-top: 7px;
}
#table_container{
    display: flex;
    flex-direction: row;
    justify-content: space-evenly;
}
#main-tb{
    width: 100%;
}

</style>