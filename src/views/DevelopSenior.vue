<script setup>
import CodeView from "@/components/CodeView.vue";
import { Delete, Edit, Download, Share, Upload } from '@element-plus/icons-vue'
import axios from "axios";
import {onMounted, ref} from "vue";
import mqtt from 'mqtt';
const options = ref([]);
const value = ref('')
import { Codemirror } from "vue-codemirror";
import { javascript } from "@codemirror/lang-javascript";
import { oneDark } from "@codemirror/theme-one-dark";
import {ElMessage} from "element-plus";

const code = ref(``);


const extensions = [javascript(), oneDark];
const height = '500px'; // 你可以根据需要设置高度

const log = console.log;







const client = mqtt.connect('mqtt://124.222.3.251:8083/mqtt'); // 使用一个公共的 MQTT 代理

const message = ref('');

client.on('connect', () => {
  console.log('Connected to MQTT broker');
  client.subscribe('CLIENT/data');
  client.subscribe('CLIENT/control');


});

client.on('message', (topic, payload) => {

  message.value = payload.toString();

  if(topic=="CLIENT/data"){

      code.value = payload.toString()

  }
  if(topic=="CLIENT/control"){
     const data = JSON.parse(payload)
     if(data.result===200){

       ElMessage({
         message: '写入完成',
         type: 'success',
       })

     }else {

       ElMessage({
         message: '发生错误',
         type: 'success',
       })



     }



  }

});

const publishMessage = () => {
  client.publish('topic/test', 'Hello, MQTT!');
};

const onchane = () =>{

  console.log("change",value)
  client.publish("WEB/control",'{"seniorId":'+value.value+'}')


}



const reSetCode = () =>{


  if(value.value===''){

    ElMessage({
      message: '请先选择传感器',
      type: 'error',
    })



  }else {





     code.value= '########################################\n' +
         '# Gfly Cloud Programing Platform\n' +
         '# coding Example\n' +
         '# @2024  Hevttc  Gao Xvyang\n' +
         '# Web shilight.cn\n' +
         '#####################################\n' +
         'import random\n' +
         'import re\n' +
         'import sys\n' +
         'import time\n' +
         'from math import sqrt, cos, sin, radians\n' +
         'from time import sleep\n' +
         '\n' +
         'import wiringpi\n' +
         'from wiringpi import GPIO\n' +
         '\n' +
         'import requests\n' +
         '\n' +
         '# pls OverWrite this value to config the Senior\n' +
         '\n' +
         'name = "温度"  # The name of Senior\n' +
         'va = None  # The Value of Senior\n' +
         'units = "℃"  # Senior Unit\n' +
         'fre = 1  # Simple Rate\n' +
         'isopen = False  # if you want use this Senior pls change to True\n' +
         '\n' +
         '### 变量定义\n' +
         'pin = 16\n' +
         '\n' +
         '\n' +
         '##################  END ######################\n' +
         '# 基于 warrypi 进行编程\n' +
         '\n' +
         '########################### 自定义 函数##############################\n' +
         '\n' +
         'def init():\n' +
         '    ## 用户代码开始段##########################################\n' +
         '    # ******************************************************#\n' +
         '    print("传感器初始化")\n' +
         '\n' +
         '\n' +
         '\n' +
         '    # ******************************************************#\n' +
         '    ## 用户代码结束段##########################################\n' +
         'def loop():\n' +
         '    global va\n' +
         '    ## 用户代码开始段##########################################\n' +
         '    # ******************************************************#\n' +
         '    print("传感器数据获取")\n' +
         '\n' +
         '\n' +
         '\n' +
         '     \n' +
         '\n' +
         '    ## 用户代码结束段##########################################\n' +
         '    dataUpLoad()\n' +
         '    time.sleep(1.0 / fre)\n' +
         '\n' +
         '\n' +
         'def read_jwd():\n' +
         '    try:\n' +
         '        with open("config/coordinates.txt", "r") as f:\n' +
         '            coordinates = f.read().strip()\n' +
         '            latitude, longitude = map(float, coordinates.split(","))\n' +
         '            return latitude, longitude\n' +
         '    except FileNotFoundError:\n' +
         '        print("Error: The file \'coordinates.txt\' does not exist.")\n' +
         '        return None, None\n' +
         '    except ValueError:\n' +
         '        print("Error: Invalid coordinate format.")\n' +
         '        return None, None\n' +
         '\n' +
         '\n' +
         'def dataUpLoad():\n' +
         '    # 定义 URL\n' +
         '    url = \'http://127.0.0.1:8080/api/seniorUpload\'\n' +
         '    # 获取当前传感器ID\n' +
         '    script_name = sys.argv[0]\n' +
         '    r = re.findall("Senior(.*?).py", script_name)\n' +
         '    # 获得经纬度标识\n' +
         '\n' +
         '    # 定义请求数据\n' +
         '    data = {\n' +
         '        "seniorId": r[0],\n' +
         '        "valueName": name,\n' +
         '        "JD": 0,\n' +
         '        "WD": 0,\n' +
         '        "sampleTime": time.time() * 1000,  # 你的时间戳\n' +
         '        "valueData": va,\n' +
         '        "valueUnits": units\n' +
         '    }\n' +
         '\n' +
         '    JD, WD = read_jwd();\n' +
         '    data[\'JD\'] = JD\n' +
         '    data[\'WD\'] = WD\n' +
         '\n' +
         '    requests.post(url, data=data)\n' +
         '\n' +
         '\n' +
         'if open:\n' +
         '    init()\n' +
         'while True:\n' +
         '    if isopen is not True:\n' +
         '        break\n' +
         '    loop()\n'


  }
}

const onupload = () =>{

  console.log("change",value)
  if(value.value===''){

    ElMessage({
      message: '请先选择传感器',
      type: 'error',
    })



  }else {

    client.publish("WEB/data",code.value);

  }




}

const onsave = () =>{

  if(value.value===''){

    ElMessage({
      message: '请先选择传感器',
      type: 'error',
    })



  }else {


    const blob = new Blob([code.value], {type: 'text/plain'}); // 创建一个 Blob 对象
    const url = URL.createObjectURL(blob); // 创建 URL 对象

    // 创建一个链接元素
    const link = document.createElement('a');
    link.href = url;
    link.download = 'senior' + value.value + '.py'; // 下载的文件名

    // 将链接元素添加到文档中
    document.body.appendChild(link);

    // 模拟点击链接以触发下载
    link.click();

    // 删除链接元素
    document.body.removeChild(link);

    // 释放 URL 对象
    URL.revokeObjectURL(url);
  }



}


const reGet = () =>{

  if(value.value===''){

    ElMessage({
      message: '请先选择传感器',
      type: 'error',
    })



  }else {

    client.publish("WEB/data",code.value);

  }


}

onMounted(async ()=>{

  try {
    const response = await axios.get('http://localhost:8080/api/getSeniors'); // 替换为你的API地址
    options.value  = response.data.data
    console.log(response.data)

  } catch (error) {
    console.error('Error:', error);
  }





})






</script>

<template>



  <el-row  justify="start" style="padding: 10px">

    <el-col :span="5">
      <el-select
          v-model="value"
          @change="onchane"
          placeholder="Select"
          style="width: 240px ;float:none;"

      >
        <el-option
            v-for="item in options"
            :key="item.seniorId"
            :label="item.seniorName"
            :value="item.seniorId"
        />
      </el-select>


    </el-col>
    <el-col :span="2" style="display: flex">
      <el-button type="primary"  @click="onsave">
        下载代码<el-icon class="el-icon--right"><Edit /></el-icon>
      </el-button>
      <el-button type="primary" @click="reSetCode">
        重置代码<el-icon class="el-icon--right"><Delete /></el-icon>
      </el-button>
      <el-button type="primary" @click="reGet">
        重新读取<el-icon class="el-icon--right"><Download /></el-icon>
      </el-button>
      <el-button type="primary" @click="onupload">
        上传代码<el-icon class="el-icon--right"><Upload /></el-icon>
      </el-button>

    </el-col>


  </el-row>


  <el-row>
    <el-col :span="24" style="padding: 10px">



      <codemirror class="custom-codemirror"

                  v-model="code"
                  placeholder="Code goes here..."
                  :autofocus="true"
                  :indent-with-tab="true"
                  :tabSize="2"
                  :height="height"
                  :extensions="extensions"
                  @ready="log('ready', $event)"
                  @change="log('change', $event)"
                  @focus="log('focus', $event)"
                  @blur="log('blur', $event)"

                  style="height: 540px"
      />
    </el-col>
  </el-row>







</template>



<style scoped>


.custom-codemirror {
  width: 500px; /* 设置宽度 */
  height: 300px; /* 设置高度 */
}

</style>