<script setup>

import MapContainer from "@/components/MapContainer.vue";

import mqtt from 'mqtt';
import { ref } from 'vue';

const customColors = [
  { color: '#f56c6c', percentage: 20 },
  { color: '#e6a23c', percentage: 40 },
  { color: '#5cb87a', percentage: 60 },
  { color: '#1989fa', percentage: 80 },
  { color: '#6f7ad3', percentage: 100 },
]





/// 总览界面  主要服务

// 1 MQTT客户端
   // 接收飞机端发送的数据

// 2飞机位置显示
   // (1) 显示飞机实时位置
   // (2) 飞机处于飞行状态时，记录飞机轨迹为线条

// 3天气数据显示
   // 更具 GPS 数据坐标位置，获取当前位置的天气状况



// MQTT实现

const client = mqtt.connect('mqtt://124.222.3.251:8083/mqtt'); // 使用一个公共的 MQTT 代理

const message = ref('');

client.on('connect', () => {
  console.log('Connected to MQTT broker');
  client.subscribe('topic/test');
});

client.on('message', (topic, payload) => {
  message.value = payload.toString();
});

const publishMessage = () => {
  client.publish('topic/test', 'Hello, MQTT!');
};







</script>

<template style="padding: 20px">

  <div class="d_content">
    <h1>总览界面</h1>

    <el-row>
      <el-col :span="12">

        <el-card class="box-card">
          <template #header>
            <div class="card-header">
              <span style="color: #f2f2f2">我的飞行器</span>
            </div>
          </template>
          <div>
            <el-row>
              <el-col :span="12" style="text-align:center">
                <el-progress type="circle" :percentage="10"  :color="customColors "/>
              </el-col>
              <el-col :span="12" style="color: #f2f2f2">
                <p>飞机温度: 16°C</p>
                <p>飞机海拔: 820m</p>
                <p>位置: 河北省张家口市</p>

              </el-col>
            </el-row>
            <el-row>
              <el-col :span="12" style="text-align:center"> <p style="color: #f2f2f2">电池电量</p></el-col>
              <el-col :span="12"></el-col>
            </el-row>
          </div>
        </el-card>

        <el-card class="box-card" style="  margin-top: 10px;">
          <template #header>
                <div class="card-header">
                  <span style="color: #f2f2f2">天气状况</span>
                </div>
              </template>
              <div >
                <p style="font-size: 20px;color: #f2f2f2">秦皇岛市 海港区</p>
                <p style="font-size: 16px;color: #f2f2f2">-6 °C 晴朗</p>
                <p style="font-size: 16px;color: #f2f2f2">西北风 3级</p>


              </div>
            </el-card>






      </el-col>
      <el-col :span="12">

        <MapContainer/>







      </el-col>
    </el-row>





  </div>




</template>

<style scoped>
.d_content{

  margin: 10px;




}
.card-header {
  font-size: 15px;
  height: 15px;
}

.box-card {
  padding: 5px;
  background: #F4A261;
  width: 450px;

}



</style>