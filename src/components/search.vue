<template>
    <div>
        <div>
            <h5>Search format: year/month/date/hour minute.csv ex.<span id="read-me"> 2020/7/23/0/,1.csv</span></h5>
        </div>
        <div class="search-area">
            <input v-model="msg" placeholder="Search me :)">
            <button v-on:click="log_msg" id="search-btn">Go!</button>
        </div>
    </div>
</template>
<script>
import axios from 'axios'
export default {
    name:'search',
    data:function () {
        return{
            msg:"",
            is_search:false,
            data:[],
            data_dst:[],
            header:[]
        }
    },
    methods:{
        log_msg:function() {
            let post_data = this.msg.split(",")
            axios.post("http://158.108.183.253:8080/api/csv_test",`nb_display=10&path=${post_data[0]}&file=${post_data[1]}`)
            .then(res => {
                console.log(res.data)
                if(res.data.error_status){
                    alert("please check you query!!!")
                    this.is_search = false
                }
                else{
                    this.data = res.data.data_raw
                    this.data_dst = res.data.data_raw_dst
                    this.header = res.data.header_data
                    this.is_search = true
                }
            })
            .catch(err => console.log(err))
        }
    },
    watch:{
        is_search:function() {
            console.log(this.data_dst)
            this.$emit('set-search',this.is_search)
            this.$emit("get-session","custom query: "+this.msg)
            this.$emit("get-search",[this.data,this.header])
            this.$emit("get-dst",this.data_dst)
            this.msg=""
        },
    }
}
</script>
<style>
.search-area{
    display: flex;
    flex-direction: row;
    justify-content: center;
}
#search-btn{
    margin-left: 10px;
}
#read-me{
    color: red;
    font-weight: bold;
}
</style>