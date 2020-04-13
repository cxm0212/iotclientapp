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
    title:'电压(V)',
    width:'30%'
  },
  {
    title: "时间",
    dataIndex: "time",
    width:'30%',
  }
];


import axios from 'axios'
// import bus from "../components/common";

export default {
  name: "HistoryVoltageTable",
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
  // mounted(){
  //    bus.$on("device", device => {
  //      this.device=device;
  //      console.log(JSON.stringify(this.device)+"----------------------------ddd")
  //    })
  // },
  watch:{
    currentDevice(value){
      console.log(JSON.stringify(value+"----------------------------------dddddddddddddd"))
    }
  },
  methods:{
   
    
  }
};
</script>
<style scoped>
.btn {
  margin-left: 5px;
}
</style>