<template>
    <div id="table_container">
        <table id="main_tb" v-if="!error_msg">
            <tr>
                <th v-for="hdr in hdr_data" :key="hdr">{{hdr}}</th>
            </tr>
            <tr v-for="n in 10" :key="n">
                <td>{{ip_addr[n-1]}}</td>
                <td>{{port[n-1]}}</td>
                <td>{{ip_version[n-1]}}</td>
                <td>{{end_point[n-1]}}</td>
                <td>{{count[n-1]}}</td>
                <td>{{sum_size[n-1]}}</td>
                <td>{{packet_s[n-1]}}</td>
                <td>{{tp[n-1]}}</td>
            </tr>
        </table>
        <h1 v-else>Loading....</h1>
    </div>
</template>
<script>
import axios from 'axios'
export default {
    name:"tables",
    data(){
        return{
            hdr_data:[],
            ip_addr:[10],
            port:[10],
            ip_version:[10],
            end_point:[10],
            count:[10],
            sum_size:[10],
            packet_s:[10],
            tp:[10],
            error_msg:true,
            session_name:""
        }
    },
    mounted: function(){
        this.fetch_data()
    },
    beforeDestroy(){
        clearInterval(this.intervalid2)
    },
    methods:{
        json_data_to_local:function(str,id){
            let dt_array = str.split(",")
            this.ip_addr[id] = (dt_array[0])
            this.port[id] = (dt_array[1])
            this.ip_version[id] = ((dt_array[2] == 0 ? "ipV4" : "ipV6"))
            this.end_point[id]=((dt_array[3] == 0 ? "src" :"dst"))
            this.count[id]=parseFloat(dt_array[4]).toFixed(0)
            this.sum_size[id]=parseFloat(dt_array[5]).toFixed(0)
            this.packet_s[id]=parseFloat(dt_array[6]).toFixed(2)
            this.tp[id]=parseFloat(dt_array[7]).toFixed(2) 
        },
        fetch_data:function(){
            this.intervalid2 = setInterval(()=>{
                axios.get("http://158.108.183.253:8080/api/brief_stat")
                    .then(response =>{
                        if(!response.data.error_status){
                            //console.log(response.data)
                            this.session_name = response.data.session_name
                            this.hdr_data = response.data.header1.split(",")
                            let data_arr= response.data.data1
                            for (let index = 0; index < data_arr.length; index++) {
                                this.json_data_to_local(data_arr[index],index)
                            }
                            this.error_msg = false
                        }
                        else{
                            this.error_msg = true
                        }
                    })
                    .catch(err=>console.log(err))
            },10*1000)
        }
    },
    watch:{
        session_name:function(){
            this.$emit('session-change',this.session_name)
        }
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