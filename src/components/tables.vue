<template>
    <div id="table_container">
        <div id="table_area_src" v-if="!error_msg">
            <h3>ip src statistic</h3>
            <table id="main_tb" v-if="!error_msg">
                <tr>
                    <th v-for="hdr in hdr_data" :key="hdr"><div class="element" v-on:click="test_click(hdr,'src')">{{hdr}}</div></th>
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
                    <th v-for="hdr in hdr_data2" :key="hdr"><div class="element" v-on:click="test_click(hdr,'dst')">{{hdr}}</div></th>
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
            hdr_data:[],hdr_data2:[],
            ip_addr:[10],ip_dst:[10],
            port:[10],port_dst:[10],
            ip_version:[10],ip_ver_dst:[10],
            end_point:[10],
            count:[10],pps_dst:[10],
            sum_size:[10],tp_dst:[10],sum_data:[2],
            error_msg:true,change_pps_src:false,change_tp_src:false,change_pps_dst:false,change_tp_dst:false,
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
            if(!this.change_pps_src){
                this.count[id]=parseFloat(dt_array[4]).toFixed(2)
            }
            else{
                this.count[id]=((parseFloat(dt_array[4])/this.sum_data[0]) * 100).toFixed(2)
            }
            if(!this.change_tp_src){
                this.sum_size[id]=parseFloat(dt_array[5]).toFixed(2)
            }else{
                this.sum_size[id]=((parseFloat(dt_array[5])/this.sum_data[1])*100).toFixed(2)
            }
        },
        fetch_data:function(){
            this.intervalid2 = setInterval(()=>{
                axios.get("http://158.108.183.253:8080/api/brief_stat")
                    .then(response =>{
                        if(!response.data.error_status){
                            //console.log(response.data)
                            this.session_name = response.data.session_name
                            this.hdr_data = response.data.header1.filter(i => i !== "end point")
                            if(this.change_pps_src){
                                this.hdr_data[3] = "%pps"
                            }
                            if(this.change_tp_src){this.hdr_data[4] = "%Throughput"}
                            this.hdr_data2 = response.data.header1.filter(i => i !== "end point")
                            if(this.change_pps_dst){this.hdr_data2[3] = "%pps"}
                            if(this.change_tp_dst){this.hdr_data2[4] = "%Throughput"}
                            let data_arr= response.data.data1
                            let data_dst= response.data.data2
                            this.sum_data = response.data.summ_data.map(i => parseFloat(i)/60.0)
                            for (let index = 0; index < data_arr.length; index++) {
                                this.json_data_to_local(data_arr[index],index)
                            }
                            for (let id = 0;id < data_dst.length;id++){
                                let dt = data_dst[id].split(",")
                                this.ip_dst[id] = dt[0]
                                this.port_dst[id] = dt[1]
                                this.ip_ver_dst[id] = (dt[2] == 0 ? "IPv4" : "IPv6")
                                if(this.change_pps_dst){
                                    this.pps_dst[id] = ((parseFloat(dt[4])/this.sum_data[0])*100).toFixed(2)
                                }else{
                                    this.pps_dst[id] = parseFloat(dt[4]).toFixed(2)
                                }
                                if(this.change_tp_dst){
                                    this.tp_dst[id] = ((parseFloat(dt[5])/this.sum_data[1])*100).toFixed(2)
                                }else{
                                    this.tp_dst[id] = parseFloat(dt[5]).toFixed(2)
                                }
                            }
                            this.error_msg = false
                        }
                        else{
                            this.error_msg = true
                        }
                    })
                    .catch(err=>console.log(err))
            },10*1000)
        },
        test_click:function(val,tbSrc){
            console.log(`click on ${val} table name ${tbSrc}`)
            if(val === "packet(pps)" && tbSrc === "src"){
                this.change_pps_src = true
                this.count = this.count.map(i =>((parseFloat(i)/this.sum_data[0])*100).toFixed(2))
                this.hdr_data[3] = "%pps"
            }
            else if(val === "%pps" && tbSrc === "src"){
                this.change_pps_src = false
                this.count = this.count.map(i => ((i*this.sum_data[0])/100).toFixed(2))
                this.hdr_data[3] ="packet(pps)"
            }
            else if(val === "Throughput(bps)" && tbSrc === "src"){
                this.change_tp_src = true
                this.sum_size = this.sum_size.map(i => ((parseFloat(i)/this.sum_data[1])*100).toFixed(2))
                this.hdr_data[4] = "%Throughput"
            }
            else if(val === "%Throughput" && tbSrc === "src"){
                this.change_tp_src = false
                this.sum_size = this.sum_size.map(i => ((i*this.sum_data[1])/100).toFixed(2))
                this.hdr_data[4] = "Throughput(bps)"
            }

            if (val === "packet(pps)" && tbSrc == "dst") {
                this.change_pps_dst = true
                this.pps_dst = this.pps_dst.map(i=>((parseFloat(i)/this.sum_data[0])*100).toFixed(2))
                this.hdr_data2[3] = "%pps"
            }else if(val === "%pps" && tbSrc == "dst"){
                this.change_pps_dst = false
                this.pps_dst = this.pps_dst.map(i=>((i*this.sum_data[0])/100).toFixed(2))
                this.hdr_data2[3] = "packet(pps)"
            }else if(val === "Throughput(bps)" && tbSrc === "dst"){
                this.change_tp_dst = true
                this.tp_dst = this.tp_dst.map(i=>((parseFloat(i)/this.sum_data[1])*100).toFixed(2))
                this.hdr_data2[4]="%Throughput"
            }else if(val === "%Throughput" && tbSrc === "dst"){
                this.change_tp_dst = false
                this.tp_dst = this.tp_dst.map(i=>((i*this.sum_data[1])/100).toFixed(2))
                this.hdr_data2[4] ="Throughput(bps)"
            } 
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
.element:hover{
    background-color: rgb(255, 123, 0);
}
</style>