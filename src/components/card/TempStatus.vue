<template>
<div class="div1">
    <div class="div2">
        <div class="icondiv">
            <a-icon type='line-chart' style="color:white;font-size:60px"></a-icon>
        </div>
        <div style="text-align:'center';margin-left:60px;">
            <div style="font-size:14px;margin-top:0">设备实时温度</div>
            <div style="font-size:30px;font-weight:550">{{value}}℃</div>
        </div>
        <a-divider style="margin-top:12px;margin-bottom:0;font-size:12px;padding:10px 5px 0 5px">数据更新时间</a-divider>
        <div style="fontsize:12px">{{time}}</div>
    </div>
    
</div>

</template>

<script>
export default {
    name:'TempStatusCard',
    data(){
        return{
            time:'--',
            value:'--'
        }
    },
  
}
</script>
<style  scoped>


    .icondiv{
        position: absolute;
        top: -20px;
        left: 40px;
        width: 100px;
        height: 100px;
        background: rgb(94, 158, 243);
        border-radius: 4px;
        box-shadow: 0 0 10px grey;
        display: flex;
        align-items: center;
        justify-content: center;
    }
    .div1{
        display: flex;
        align-items: center;
        padding-top: 5%;
        justify-content: center;
        height: 100%;
        width: 100%;
    }
    .div2{
        position: relative;
        border: 1px solid #e8e8e8;
        height: 100%;
        width: 100%;
        border-radius: 4px;
    }
</style>