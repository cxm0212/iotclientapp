<template>
  <div class="div1">
    <div class="div2">
      <div class="icondiv">
        <a-icon :type="icontype" style="color:white;font-size:60px"></a-icon>
      </div>
      <div style="text-align:'center';margin-left:60px;">
        <div style="font-size:14px;margin-top:0">设备在线状态</div>
        <div style="font-size:30px;font-weight:550">{{statusInfo[deviceStatus]}}</div>
      </div>
      <a-divider
        style="margin-top:12px;margin-bottom:0;font-size:12px;padding:10px 5px 0 5px"
      >最近上线时间</a-divider>
      <div style="fontsize:12px">{{time?time:'暂未上线'}}</div>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "OnlineStatusCard",
  data() {
    return {
      time: "--",
      statusInfo:{ONLINE:'在线',OFFLINE:'离线',UNACTIVE:'设备未激活',DISABLE:'设备禁用',unselected:'--'},
      deviceStatus:'unselected',
      icontype: "disconnect",
      device: {}
    };
  },
  watch: {
    deviceStatus: function(value) {
    //   console.log("-------------------------"+value)
      return value == "ONLINE"? (this.icontype = "link"): (this.icontype = "disconnect");
    },
    device: function(value) {
      var self = this;
      axios.get("http://47.94.170.246:7890/getdevicedetail", {
          params: {
            deviceName: value.deviceName,
            productKey: value.productKey,
            iotId: value.iotId
          }
        })
        .then(function(res) {
            var data = res.data;
            self.deviceStatus = data.status;
            self.time = self.dateformatter(data.utcOnline);
            // console.log("设备详情 : "+ JSON.stringify(res))
        })
        .catch(function(err) {
            console.error("获取失败 : "+err);
            
        });
    }
  },
  methods: {
    changestate() {
      this.deviceStatus = "UNACTIVE";
    },
    dateformatter(value) {
      if (value == undefined) {
          return "";
      }
      /*json格式时间转js时间格式*/
      var date = new Date(value);
      return date.getFullYear() + '-' + (date.getMonth() + 1) + '-' +
          date.getDate() + '  ' + date.getHours() + ":" + date.getMinutes();
    }
  }
};
</script>

<style scoped>
.icondiv {
  position: absolute;
  top: -20px;
  left: 40px;
  width: 100px;
  height: 100px;
  background: #ed7010;
  border-radius: 4px;
  box-shadow: 0 0 10px grey;
  display: flex;
  align-items: center;
  justify-content: center;
}
.div1 {
  display: flex;
  align-items: center;
  padding-top: 5%;
  justify-content: center;
  height: 100%;
  width: 100%;
}
.div2 {
  position: relative;
  border: 1px solid #e8e8e8;
  height: 100%;
  width: 100%;
  border-radius: 4px;
}
</style>