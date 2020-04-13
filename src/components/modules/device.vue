<template>
  <a-layout id="components-layout-demo-top" class="layout">
    <a-layout-header>
      <div :style="{color:'white',float:'left',fontWeight:'bold',fontSize:'24px'}">智能设备监控</div>
      <div style="color:white;alignItem:center;justifyContent:'space-between';float:right;">
        请选择设备deviceName : 
        &nbsp;
        <a-select labelInValue style="width: 300px" @change="changeitem">
          <a-select-option v-for="item in deviceList" :key="item.iotId">{{item.deviceName}}</a-select-option>
          <!-- <a-select-option value="lucy">Lucy (101)</a-select-option> -->
        </a-select>
      </div>
    </a-layout-header>
    <a-layout-content style="padding: 0 50px;">
      <!-- <a-alert type="error" message="获取失败,检查设备有无此属性!" banner  " /> -->
      
      <div :style="{ background: '#fff', padding: '20px', minHeight: '280px' }">
        <div style="margin-bottom:5px;">
          <a-input-number id="inputNumber"  v-model="setvalue"/>
          <a-button type='primary' @click="setVoltage" style="margin-left:5px;">设置电压</a-button>
        </div>
        <a-card title="设备当前状态" >
          <a-card-grid style="width:25%;text-align:center;height:200px;">
            <online-status-card ref="onlineChildRef" />
          </a-card-grid>
          <a-card-grid style="width:25%;text-align:center;height:200px;">
            <voltage-status-card ref="voltageChildRef" />
          </a-card-grid>
          <a-card-grid style="width:25%;text-align:center;height:200px;">
            <temp-status-card ref="tempChildRef" />
          </a-card-grid>
          <a-card-grid style="width:25%;text-align:center;height:200px;">
            <electricity-status-card ref="electricityChildRef" />
          </a-card-grid>
        </a-card>
      </div>
      <div>
        <a-card
          style="width:100%"
          :tabList="tabList"
          :activeTabKey="key"
          @tabChange="key => onTabChange(key, 'key')"
        >
          <span slot="customRender" slot-scope="item">{{item.key}}</span>

          <!-- <div>{{contentList[key]}}</div> -->
          <!-- 根据标题动态改变组件 -->
          <!-- <component :is="dataComponent" :currentDevice="currentDevice"></component> -->

          <div style="margin-left:10px;">
            <span style="width:50%;">
              <label>选择查询时间 : </label>
              <a-range-picker v-model="value" />

              <a-button class="btn" type="primary" @click="submit">提交</a-button>
            </span>
          </div>
          <div style="margin-top:10px;">
            <a-table :columns="columns" :dataSource="data"  bordered></a-table>

            <!-- <a-table style="float:left;width:33%" :columns="CurrentTemperature_columns" :dataSource="CurrentTemperature_data"  bordered></a-table>
            <a-table style="float:left;width:33%" :columns="OutputVoltage_columns" :dataSource="OutputVoltage_data"  bordered></a-table>
            <a-table style="float:left;width:33%" :columns="OutputCurrent_columns" :dataSource="OutputCurrent_data"  bordered></a-table> -->

          </div>
        </a-card>
      </div>
    </a-layout-content>
    <a-layout-footer style="text-align: center">
      Ant Design ©2020 
      <!-- <charts-card /> -->
    </a-layout-footer>
  </a-layout>
</template>
<style scoped>
#components-layout-demo-top .logo {
  width: 120px;
  height: 31px;
  background: rgba(255, 255, 255, 0.2);
  margin: 16px 24px 16px 0;
  float: left;
}
.btn {
  margin-left: 5px;
}
/* .hidealertmsg{
  display: none;
}
.showalertmsg{
  width: 400px;
  display:block;
  position:absolute;
  top: 20%;
  left: 45%;
} */
</style>


<script>
// import Vue from 'vue'
import OnlineStatusCard from "../card/onlinestatus.vue";
import VoltageStatusCard from "../card/VoltageStatus.vue";
import TempStatusCard from "../card/TempStatus.vue";
import ElectricityStatusCard from "../card/ElectricityStatus.vue";
// import ChartsCard from "../Charts.vue";
// import HistoryVoltageTable from "../HistoryVoltageTable.vue";
// import HistoryElectricityTable from "../HistoryElectricityTable"
// import HistoryTempureTable from "../HistoryTempureTable"

import axios from "axios";

// 巴士：用于兄弟组件间传值
// import bus from "../common";

var websocket = null;

export default {
  components: {
    OnlineStatusCard,
    VoltageStatusCard,
    TempStatusCard,
    ElectricityStatusCard
    // ChartsCard,
    // HistoryVoltageTable,
    // HistoryElectricityTable,
    // HistoryTempureTable
  },

  data() {
    return {
      collapsed: false,
      tabList: [
        {
          key: "tab1",
          tab: <a-tooltip title="设备历史数据查询">设备历史数据</a-tooltip>
        },
        // {
        //   key: "tab2",
        //   tab: <a-tooltip title="设备历史电压记录查询">设备历史电压</a-tooltip>
        // },
        // {
        //   key: "tab3",
        //   tab: <a-tooltip title="设备历史电流记录查询">设备历史电流</a-tooltip>
        // }
      ],
      // dataComponent: HistoryTempureTable,

      key: "tab1",
      lastTime: "2010-10-10 12:20:30",
      setvalue:"",
      // extratext: (
      //   <div>
      //     <a-button type='primary' onClick="setVoltage">设置电压</a-button>
      //     <a-input-number id="inputNumber"  v-model="setvalue"/>
      //   </div>
      // ),  
          
      deviceList: [],
      currentDevice: {},
      columns:[
        {
          dataIndex: "CurrentTemperature_value",
          title: "温度(℃)",
          width: "15%"
        },
        {
          title: "温度更新时间",
          dataIndex: "CurrentTemperature_time",
          width: "15%"
        },
        {
          dataIndex: "OutputVoltage_value",
          title: "电压(V)",
          width: "15%"
        },
        {
          title: "电压更新时间",
          dataIndex: "OutputVoltage_time",
          width: "15%"
        },
        {
          dataIndex: "OutputCurrent_value",
          title: "电流(A)",
          width: "15%"
        },
        {
          title: "电流更新时间",
          dataIndex: "OutputCurrent_time",
          width: "15%"
        },
      ],
      data:[],
      // CurrentTemperature_columns: [
      //   {
      //     dataIndex: "value",
      //     title: "温度(℃)",
      //     width: "30%"
      //   },
      //   {
      //     title: "温度更新时间",
      //     dataIndex: "time",
      //     width: "30%"
      //   },
        
        
      // ],
      // OutputVoltage_columns:[
      //   {
      //     dataIndex: "value",
      //     title: "电压(V)",
      //     width: "30%"
      //   },
      //   {
      //     title: "电压更新时间",
      //     dataIndex: "time",
      //     width: "30%"
      //   },
      // ],
      // OutputCurrent_columns:[{
      //     dataIndex: "value",
      //     title: "电流(A)",
      //     width: "300"
      //   },
      //   {
      //     title: "电流更新时间",
      //     dataIndex: "time",
      //     width: "300"
      //   }],
        
      // CurrentTemperature_data: [],
      // OutputVoltage_data: [],
      // OutputCurrent_data: [],
      pagination: {},
      value: [],
      identifier: 'CurrentTemperature,OutputVoltage,OutputCurrent'
    };
  },

  methods: {
    getDeviceList: function() {
      var self = this;
      axios
        .get("http://47.94.170.246:7890/finddevicelistbyproduct", {
          params: {
            //传参
            productKey: "a1ppUE8HllR"
          }
        })
        .then(function(res) {
          // console.log("get请求返回数据 : "+  JSON.stringify(res));
          self.deviceList = res.data;
        })
        .catch(function(err) {
          console.log("请求失败233" + err);
        });
    },
    changeitem: function(value) {
      console.log(value);
      var device = {};
      this.deviceList.forEach(item => {
        if (item.iotId == value.key) {
          device = item;
        }
      });
      this.$refs.onlineChildRef.device = device;
      this.currentDevice = device;

      this.queryDevicePropertiesData(device, this);
      websocket.send(JSON.stringify({ type: 1, iotId: device.iotId }));

      this.data=[];
      this.value=[];
    },
    onTabChange(key, type) {
      console.log(key, type);
      this[type] = key;

      this.data = [];
      this.value = [];
      if (key == "tab1") {
        // this.dataComponent = HistoryTempureTable
        this.columns = [
          {
            dataIndex: "value",
            title: "温度(℃)",
            width: "30%"
          },
          {
            title: "时间",
            dataIndex: "time",
            width: "30%"
          }
        ];
        this.identifier = "CurrentTemperature";
      } else if (key == "tab2") {
        // this.dataComponent=HistoryVoltageTable
        this.columns = [
          {
            dataIndex: "value",
            title: "电压(V)",
            width: "30%"
          },
          {
            title: "时间",
            dataIndex: "time",
            width: "30%"
          }
        ];
        this.identifier = "OutputVoltage";
      } else if (key == "tab3") {
        // this.dataComponent=HistoryElectricityTable
        this.columns = [
          {
            dataIndex: "value",
            title: "电流(A)",
            width: "30%"
          },
          {
            title: "时间",
            dataIndex: "time",
            width: "30%"
          }
        ];
        this.identifier = "OutputCurrent";
      }
    },
    queryDevicePropertiesData: function(device, self) {
      self.$refs.voltageChildRef.value = "--";
      self.$refs.voltageChildRef.time = "--";

      self.$refs.tempChildRef.value = "--";
      self.$refs.tempChildRef.time = "--";

      self.$refs.electricityChildRef.value = "--";
      self.$refs.electricityChildRef.time = "--";

      axios
        .get("http://47.94.170.246:7890/queryDevicePropertiesData", {
          params: {
            deviceName: device.deviceName,
            productKey: device.productKey,
            iotId: device.iotId
          }
        })
        .then(function(res) {
          var data = res.data;
          self.$refs.voltageChildRef.value = data.outputVoltage;
          self.$refs.voltageChildRef.time = data.voltageTime;

          self.$refs.tempChildRef.value = data.currentTemperature;
          self.$refs.tempChildRef.time = data.temperatureTime;

          self.$refs.electricityChildRef.value = data.outputElectricity;
          self.$refs.electricityChildRef.time = data.electricityTime;

          // self.isShowAlertMsg = 1;
        })
        .catch(function(err) {
          alert("获取失败,检查设备由无此属性!");
          console.error("获取失败 : " + err);
        });
    },
    createWS: function(self) {
      if ("WebSocket" in window) {
        websocket = new WebSocket("ws://47.94.170.246:7890/ws");
      } else {
        console.info("该浏览器不支持websocket", { icon: 2, time: 1000 });
      }
      websocket.onopen = function(event) {
        console.info("建立连接" + event);
        setInterval(function() {
          websocket.send({ type: 0 });
        }, 1000 * 60 * 10);
      };
      websocket.onclose = function(event) {
        console.info("关闭连接" + event);
      };
      websocket.onmessage = function(event) {
        console.info("收到消息" + event.data);
        var data = JSON.parse(event.data);
        if(data.type=="deviceProperty"){
          if (data.outputVoltage != null && data.outputVoltage != "") {
            self.$refs.voltageChildRef.value = data.outputVoltage;
            self.$refs.voltageChildRef.time = data.voltageTime;
          }
          if (data.currentTemperature != null && data.currentTemperature != "") {
            self.$refs.tempChildRef.value = data.currentTemperature;
            self.$refs.tempChildRef.time = data.temperatureTime;
          }
          if (data.outputElectricity != null && data.outputElectricity != "") {
            self.$refs.electricityChildRef.value = data.outputElectricity;
            self.$refs.electricityChildRef.time = data.electricityTime;
          }
        }else if(data.type=="deviceStatus"){
          self.$refs.onlineChildRef.deviceStatus = data.status;
        }

      };
      websocket.onerror = function() {
        console.info("websocket通讯发生错误！");
      };
      websocket.onbeforeunload = function() {
        websocket.close();
      };
    },
    submit: function() {  
      var self = this;
      if (this.value.length == 0) {
        alert("请选择查询时间!");
        return;
      }
      axios
        .get("http://47.94.170.246:7890/querydevicepropertyhistorydata", {
          params: {
            deviceName: this.currentDevice.deviceName,
            productKey: this.currentDevice.productKey,
            iotId: this.currentDevice.iotId,
            identifier: this.identifier,
            startTime: this.value[0].format("YYYY-MM-DD HH:MM:SS"),
            endTime: this.value[1].format("YYYY-MM-DD HH:MM:SS"),
            asc: 0,
            pageSize: "50"
          }
        })
        .then(function(res) {
          // console.log(JSON.stringify(res)+"----------------------返回数据")
          self.data = res.data;
          // var data = res.data;
          // self.CurrentTemperature_data = data.CurrentTemperature;
          // self.OutputVoltage_data = data.OutputVoltage;
          // self.OutputCurrent_data = data.OutputCurrent;
        })
        .catch(function(err) {
          console.error("获取失败 : " + err);
        });
    },
    setVoltage : function(){
      if(this.currentDevice.deviceName ==null){
        alert("请选择设备!");
        return;
      }
      this.setDeviceProperty({"OutputVoltage":this.setvalue});
    },
    setDeviceProperty : function(item){
      console.log(item+"---------------------------设置设备数据")
      axios.get("http://47.94.170.246:7890/setDeviceProperty",{
          params:{
            deviceName: this.currentDevice.deviceName,
            productKey: this.currentDevice.productKey,
            iotId: this.currentDevice.iotId,
            propertyItems : JSON.stringify(item)
          }
      })
    }
  },
  mounted() {
    this.getDeviceList();
    this.createWS(this);
  }
};
</script>
