<template>
    <div id="table_container">
        <div id="table_area_src" v-if="!error_msg">
            <h3>ip src statistic</h3>
            <table id="main_tb" v-if="!error_msg">
                <tr>
                    <th v-for="hdr in hdr_data" :key="hdr">{{hdr}}</th>
                </tr>
                <tr v-for="n in 10" :key="n">
                    <td>{{ip_addr[n-1]}}</td>
                    <td>{{port[n-1]}}</td>
                    <td>{{ip_version[n-1]}}</td>
                    <td>{{count[n-1]}}</td>
                    <td>{{sum_size[n-1]}}</td>
                </tr>
            </table>
        </div>
        <div id="table_area_src" v-if="!error_msg">
            <h3>ip dst statistic</h3>
            <table id="main_tb" v-if="!error_msg">
                <tr>
                    <th v-for="hdr in hdr_data" :key="hdr">{{hdr}}</th>
                </tr>
                <tr v-for="n in 10" :key="n">
                    <td>{{ip_dst[n-1]}}</td>
                    <td>{{port_dst[n-1]}}</td>
                    <td>{{ip_ver_dst[n-1]}}</td>
                    <td>{{pps_dst[n-1]}}</td>
                    <td>{{tp_dst[n-1]}}</td>
                </tr>
            </table>
        </div>
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
            ip_addr:[10],ip_dst:[10],
            port:[10],port_dst:[10],
            ip_version:[10],ip_ver_dst:[10],
            end_point:[10],
            count:[10],pps_dst:[10],
            sum_size:[10],tp_dst:[10],
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
            this.ip_version[id] = ((dt_array[2] == 0 ? "IPv4" : "IPv6"))
            this.end_point[id]=((dt_array[3] == 0 ? "src" :"dst"))
            this.count[id]=parseFloat(dt_array[4]).toFixed(2)
            this.sum_size[id]=parseFloat(dt_array[5]).toFixed(2)
        },
        fetch_data:function(){
            this.intervalid2 = setInterval(()=>{
                axios.get("http://158.108.183.253:8080/api/brief_stat")
                    .then(response =>{
                        if(!response.data.error_status){
                            //console.log(response.data)
                            this.session_name = response.data.session_name
                            this.hdr_data = response.data.header1.filter(i => i !== "end point")
                            let data_arr= response.data.data1
                            let data_dst= response.data.data2
                            for (let index = 0; index < data_arr.length; index++) {
                                this.json_data_to_local(data_arr[index],index)
                            }
                            for (let id = 0;id < data_dst.length;id++){
                                let dt = data_dst[id].split(",")
                                this.ip_dst[id] = dt[0]
                                this.port_dst[id] = dt[1]
                                this.ip_ver_dst[id] = (dt[2] == 0 ? "IPv4" : "IPv6")
                                this.pps_dst[id] = parseFloat(dt[4]).toFixed(2)
                                this.tp_dst[id] = parseFloat(dt[5]).toFixed(2)
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
    display: flex;
    flex-direction: row;
    justify-content: space-evenly;
}
#main-tb{
    width: 100%;
}

</style>