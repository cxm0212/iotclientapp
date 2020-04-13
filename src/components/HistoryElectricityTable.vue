<template>
  <div>
    <div>
      <span style="width:50%;">
        <label>选择查询时间 :</label>
        <a-range-picker v-model="value" />

        <a-button class="btn" type="primary" @click="submit">提交</a-button>
      </span>
    </div>
    <div style="margin-top:10px;">
      <a-table :columns="columns" :dataSource="data" :pagination='pagination' bordered>
       
      </a-table>
    </div>
  </div>
</template>

<script scoped>
const columns = [
  {
    dataIndex: "value", 
    title:'电流(A)',
    width:'30%'
  },
  {
    title: "时间",
    dataIndex: "time",
    width:'30%',
  }
];


import axios from 'axios'
import bus from "../components/common";

export default {
  name: "HistoryElectricityTable",
  props:{currentDevice:Object},
  data() {
    return {
      columns,
      data:[],
      pagination:{},
      value:[],
      device:{},
    };
  },
  mounted(){
     bus.$on("device", device => {
       this.device=device;
        console.log(JSON.stringify(this.device)+"----------------------------ddd")
     })
  },
  methods:{
    submit:function(){
      var self = this;
      if(this.value.length==0){
        alert("请选择查询时间!");
        return;
      }
      axios.get("http://localhost:7890/querydevicepropertyhistorydata", {
          params: {
            deviceName: this.device.deviceName,
            productKey: this.device.productKey,
            iotId: this.device.iotId,
            identifier:'OutputCurrent',
            startTime : this.value[0].format('YYYY-MM-DD HH:MM:SS'),
            endTime:this.value[1].format('YYYY-MM-DD HH:MM:SS'),
            asc:0,
            pageSize:'50'
          }
        })
        .then(function(res) {
            console.log(JSON.stringify(res)+"----------------------返回数据")
            self.data = res.data;
        })
        .catch(function(err) {
            console.error("获取失败 : "+err);
            
        });
    },
    
  }
};
</script>
<style scoped>
.btn {
  margin-left: 5px;
}
</style>