<template>
    <div>
        <div>
            <h5>Search format: year/month/date/hour minute.csv ex. 2020/7/23/0/ 1.csv</h5>
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
            msg:""
        }
    },
    methods:{
        log_msg:function() {
            let post_data = this.msg.split(" ")
            axios.post("http://158.108.183.253:8080/api/csv_test",`nb_display=10&path=${post_data[0]}&file=${post_data[1]}`)
            .then(res => {
                console.log(res.data)
                if(res.data.error_status){
                    alert("please check you query!!!")
                }
            })
            .catch(err => console.log(err))
        }
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
</style>