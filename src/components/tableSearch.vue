<template>
    <div id="table_container">
        <table id="main_tb">
            <tr>
                <th v-for="hdr in search_hdr" :key="hdr">{{hdr}}</th>
            </tr>
            <tr v-for="n in search_data" :key="n">
                <td v-for="j in n" :key="j">{{j}}</td>
            </tr>
        </table>
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
        search_data:Array
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
    margin-top: 12px ;
    display: flex;
    justify-content: center;
}
#main-tb{
    width: 100%;
}

</style>