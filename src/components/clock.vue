<template>
    <div class="time_info">
        <h1>Server time: <span id="server_time"></span></h1>
        <p id="server_time" v-if="year !== ''">{{date}}/{{month}}/{{year}} {{hour}}:{{min}}</p>
        <p id="server_time" v-else>Loading......</p>
    </div>
</template>

<script>
import axios from 'axios'
export default {
    name: 'clock',
    data(){
        return{
            year: "",
            month: "",
            date: "",
            hour: "",
            min: ""
        }
    },
    mounted: function() {
        this.fetch_clock()
    },
    beforeDestroy(){
        clearInterval(this.intervalid1)
    },
    methods:{
        fetch_clock:function() {
            const self = this;
            this.intervalid1 = setInterval(function() {
                    axios
                        .get('http://158.108.183.253:8080/api/serv_time')
                        .then(response => {
                            let web_data = response.data
                            self.year = web_data.serv_year
                            self.month = (web_data.serv_month < 10 ? "0"+web_data.serv_month : web_data.serv_month)
                            self.date = (web_data.serv_day < 10 ? "0" + web_data.serv_day : web_data.serv_day)
                            self.hour = (web_data.serv_hour < 10 ? "0" + web_data.serv_hour : web_data.serv_hour)
                            self.min = (web_data.serv_min < 10 ? "0" + web_data.serv_min : web_data.serv_min)
                        })
                        .catch(err => console.log(err))                    
                },10*1000
            )
        }
    },
}
</script>
<style>
#server_time {
    padding-top:5px ;
    font-weight: 600;
}
</style>