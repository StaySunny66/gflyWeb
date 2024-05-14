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





     code.value='# 云上可编程平台\n' +
         '# 模板代码\n' +
         '#####################################\n' +
         '# 传感器变量编写\n' +
         'from time import sleep\n' +
         '# 请按照一下代码完成编写\n' +
         'name = ["temp1","temp2",]\n' +
         'va = [12.1,23,]\n' +
         'units = ["摄氏度","华氏度"]\n' +
         '########################################\n' +
         '\n' +
         '\n' +
         'def init():\n' +
         '    ## 用户代码开始段\n' +
         '    print("传感器初始化")\n' +
         '\n' +
         '\n' +
         '    ## 用户代码结束端\n' +
         '\n' +
         'def loop():\n' +
         '    ## 用户代码开始段\n' +
         '    print("传感器初始化")\n' +
         '\n' +
         '\n' +
         '\n' +
         '\n' +
         '\n' +
         '    ## 用户代码结束端\n' +
         '    sleep(0.1)\n' +
         '    ## 数据上报\n' +
         '\n' +
         '\n' +
         '\n' +
         '\n' +
         '\n'


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
    const response = await axios.get('http://127.0.0.1:8080/api/getSeniors'); // 替换为你的API地址
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