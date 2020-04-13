<template>
  <div>
    <div>
    <span style="width:50%;">
      <label> 选择查询时间 : </label>
      <a-range-picker>
        <div slot="dateRender" slot-scope="current">
          <div class="ant-calendar-date" :style="getCurrentStyle(current)">
           
            {{current.date()}}
            
          </div>
        
        </div>
        
      </a-range-picker>
      <a-button class="btn" type="primary">提交</a-button>
    </span>

    <span style="width:50%;margin-left:50px;" class="components-input-demo-size">
      修改温度阙值 : 
      
      <a-input />
      <a-button  type="primary">提交</a-button>
    </span>
    </div>
    <div>
     
    </div>
  </div>
</template>

<script scoped>
export default {
  name: "ChartsCard",
  methods: {
    getCurrentStyle(current) {
      const style = {};
      if (current.date() === 1) {
        style.border = "1px solid #1890ff";
        style.borderRadius = "50%";
      }
      return style;
    }
  },
  
};





</script>
<style scoped>
.btn{
  margin-left: 5px;
}
.components-input-demo-size .ant-input {
    width: 200px;
    margin: 0 5px 8px 0;
    
  }

</style>