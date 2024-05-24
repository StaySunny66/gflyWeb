<script setup>

import FlyVideo from "@/components/FlyVideo.vue";
import ValShow from "@/components/ValShow.vue";
import mqtt from 'mqtt';
import { ref } from 'vue';


const angle1 = ref(90)
const angle2 = ref(100)



const onanglechange = () =>{

  console.log('{"angle1": '+angle1+', "angle2":'+angle2+' }')

  client.publish('video', '{"angle1": '+angle1.value+', "angle2":'+angle2.value+' }');


}


/// 接收传输到网页的视频流

/// finish

/// python 标记出 人体 可能的搜救信号

///

/// 再 视频流下方 显示搜救人数等信息

/// mqtt

/// 声明响应式数据
const messages = ref([]);
const openPeople = ref(false);

/// MQTT实现

const client = mqtt.connect('mqtt://124.222.3.251:8083/mqtt'); // 使用一个公共的 MQTT 代理

const message = ref('');

client.on('connect', () => {
  console.log('Connected to MQTT broker');
  client.subscribe('Gfly001/Main');
});

client.on('message', (topic, payload) => {
  message.value =JSON.parse(payload.toString()).people;
  console.log(message.value)
});

const publishMessage = () => {
  client.publish('topic/test', 'Hello, MQTT!');
};

var people = () =>{
  if(openPeople.value){
    client.publish('people', 'on');
    console.log("on")
  }else {

    client.publish('people', 'off');
    console.log("off")

  }



}



</script>

<template>


  <el-row class="row-bg" justify="center" style="margin-top: 50px">

    <el-col :span="6"  style="margin-right: 30px" >



      <el-card style="background: rgba(255,0,0,0.3)" >
        <template #header>
          <div class="card-header">
            <span style="color: #f2f2f2 ">搜救识别数据</span>


              <el-switch   v-model="openPeople"
                           class="ml-2"
                           @change="people"
                           inline-prompt
                           style="--el-switch-on-color: #13ce66; --el-switch-off-color: #ff4949"
                           active-text="自动搜索"
                           inactive-text="关闭搜素"
              />

          </div>
        </template>

        <el-row class="row-bg" justify="center" style="text-align: center" >

          <el-col :span="12"   >
            <div>
              <p style="color: #faecd8">发现人数</p>

              <p style="color: rgba(255,0,0,0.3);font-size: 20px">{{ message }} 人</p>

            </div>

          </el-col>
          <el-col :span="12"   >

            <div>
              <p style="color: #faecd8">可疑火灾</p>

              <p style="color: rgba(255,0,0,0.3);font-size: 20px">{{ message }} 人</p>

            </div>


          </el-col>



        </el-row>









      </el-card>

      <el-card style="background: rgba(255,0,0,0.3);margin-top: 10px" >

        <template #header>
          <div class="card-header">
            <span>控制</span>
          </div>
        </template>



        <div class="slider-demo-block">
          <span class="demonstration">左右控制</span>
          <el-slider v-model="angle1"
                     @input="onanglechange"
                     :max=180
          />
        </div>

        <div class="slider-demo-block">
          <span class="demonstration">上下控制</span>
          <el-slider v-model="angle2"
                     @input="onanglechange"
                     :max=180
          />
        </div>



      </el-card>

      <el-card style="background: rgba(255,0,0,0.3);margin-top: 10px" >

        <template #header>
          <div class="card-header">
            <span>视频标记图例</span>
          </div>
        </template>
        <el-alert  title="人体标记" type="success" effect="dark" :closable="false" />
        <el-alert title="火情标记" style="margin-top: 8px;background:  rgba(255,0,0)" effect="dark" :closable="false" />
      </el-card>








    </el-col >

    <el-col :span="16" style="padding: 0 !important;">

      <el-card style="background: #E76F51">


        <FlyVideo/>


      </el-card>


    </el-col>

  </el-row>




</template>

<style scoped>








</style>