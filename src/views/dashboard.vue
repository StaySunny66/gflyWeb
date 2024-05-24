<script setup>
import mqtt from 'mqtt';
import {onMounted, onUnmounted, ref} from 'vue';
import AMapLoader from "@amap/amap-jsapi-loader";

const customColors = [
  { color: '#f56c6c', percentage: 20 },
  { color: '#e6a23c', percentage: 40 },
  { color: '#5cb87a', percentage: 60 },
  { color: '#1989fa', percentage: 80 },
  { color: '#6f7ad3', percentage: 100 },
]

let map = null
let path = []
let polyline = null
let marker = null


onMounted(() => {
  AMapLoader.load({
    key: "00fa52820d61c178fb71a42f750d30f1",
    version: "2.0",
    plugins: [],
  })
      .then((AMap) => {


        map = new AMap.Map("container", {
          center: [116.400274, 39.905812],
          zoom: 20
          // 将经纬度绑定到地图的中心点位置
        });




         polyline = new AMap.Polyline({
           path: path,
           strokeColor: "green",
           strokeWeight: 6,
           strokeOpacity: 0.9,
           zIndex: 50,
           bubble: true,
        })
        var icon = new AMap.Icon({
          image: 'http://g.shilight.cn/plane.png', // 自定义图标的 URL
          size: new AMap.Size(50, 50), // 图标大小
          imageSize: new AMap.Size(50, 50), // 图标显示尺寸
          offset: new AMap.Pixel(20, 20)
        });


       // 创建标记点并设置位置和自定义图标
        marker = new AMap.Marker({
          position: [0, 0], // 初始位置，稍后在接收到经纬度数据时更新
          icon: icon // 设置自定义图标
        });
        map.add(polyline)

        // 缩放地图到合适的视野级别
        map.setFitView()











      })
      .catch((e) => {
        console.log(e);
      });

});

onUnmounted(() => {
  map?.destroy();
});

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

client.on('connect', () => {
    console.log('Connected to MQTT broker');
    client.subscribe('PlaneState');
});

client.on('message', (topic, payload) => {
  message.value = payload.toString();


  if(topic === "PlaneState"){
      // 更新界面数据
      console.log(message.value)
      var data =  JSON.parse(payload.toString());
      JD.value = data.longitude
      WD.value = data.latitude


    // 将经纬度添加到轨迹数据中
    path.push([JD.value, WD.value]);

    // 更新轨迹线的坐标点
    if(polyline!=null){
      polyline.setPath(path);
      map.add(polyline)


      // 调用setFitView方法使地图自适应新的轨迹范围
      map.setCenter([JD.value, WD.value]);

      marker.setPosition([JD.value, WD.value]);
      map.add(marker)



    }





  }


});






const message = ref('');
const JD = ref(119.22)
const WD = ref(35.444)
const GPSTime = ref('')


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

              <el-col :span="12" style="color: #f2f2f2">

                <p>经度: {{JD}}</p>
                <p>维度: {{WD}}</p>
                <p>位置: 河北省 秦皇岛市</p>

              </el-col>
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

        <div id="container"></div>



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

#container {
  padding: 0px;
  margin: 0px;
  width: 100%;
  height: 500px;
}

</style>