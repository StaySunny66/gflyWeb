<script setup>
import {onMounted, onUnmounted, ref} from "vue";
import AMapLoader from "@amap/amap-jsapi-loader";
import axios from "axios";



// 下拉框 选择传感器
const options = ref([]);
const value = ref('')




let map = null;
let mapInstance;

let loca
let geo
let heatmap

onMounted(async () => {


  try {
    const response = await axios.get('http://127.0.0.1:8080/api/getMapSeniors'); // 替换为你的API地址
    options.value  = response.data.data
    console.log(response.data)

  } catch (error) {
    console.error('Error:', error);
  }




  AMapLoader.load({
    key: "00fa52820d61c178fb71a42f750d30f1", // 申请好的Web端开发者Key，首次调用 load 时必填
    version: "2.0",
    plugins: [],
    Loca:{                // 是否加载 Loca， 缺省不加载
      "version": '2.0.0'  // Loca 版本，缺省 1.3.2
    },
  }).then((AMap) => {




    var map = new AMap.Map('container', {
      zoom: 15,
      center: [119.54731499566,39.91358452691],
      showLabel: true,
      mapStyle: 'amap://styles/45311ae996a8bea0da10ad5151f72979',
      viewMode: '3D',
    });

     loca = new Loca.Container({
      map,
    });

  //  geo = new Loca.GeoJSONSource({
    //  url: 'https://a.amap.com/Loca/static/loca-v2/demos/mock_data/tsing.json',
   // });

    heatmap = new Loca.HeatMapLayer({
      // loca,
      zIndex: 10,
      opacity: 1,
      visible: true,
      zooms: [2, 22],
    });












  }).catch((e) => {
        console.log(e);
      });
});


const onchane = async () =>{
  console.log("change",value)

  const formData = new FormData();
  formData.append('id', 99);
  formData.append('time', 100);
  formData.append('x', 1);

  try {
    const response = await axios.post('http://127.0.0.1:8080/api/getMapData', formData) // 替换为你的API地址

    console.log(response.data)

    geo = new Loca.GeoJSONSource({
      data : response.data.data
    });

    heatmap.setSource(geo, {
      radius: 20,
      unit: 'meter',
      height: 5,
      difference: true,
      gradient: {
        1: '#FF4C2F',
        0.8: '#FAA53F',
        0.6: '#FFF100',
        0.5: '#7DF675',
        0.4: '#5CE182',
        0.2: '#29CF6F',
      },
      value: function (index, feature) {
        return feature.properties.count;
      },
      opacity: [0, 1],
      heightBezier: [0, 0.53, 0.37, 0.98]
    });
    loca.add(heatmap);




  } catch (error) {
    console.error('Error:', error);
  }

  // 获取热力图数据








}


onUnmounted(() => {
  map?.destroy();
});



</script>

<template>



  <el-row style="padding: 10px" >

    <el-col :span="4">
      <el-text style="font-size: 25px;color: #E76F51;">传感器数据热力图</el-text>
    </el-col>

    <el-col :span="4" >
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

    <el-col :span="4">







    </el-col>


  </el-row>
  <el-row justify="center">

    <el-col :span="24"  style="padding-left: 10px;padding-right: 10px;margin-top: 10px"  >
      <div id="container"></div>
    </el-col>

  </el-row>





</template>

<style scoped>
#container{
  padding:0px;
  margin: 0px;
  width: 100%;
  height: 500px;
}
</style>