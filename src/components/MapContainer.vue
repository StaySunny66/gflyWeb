<template>
  <div id="container"></div>
</template>

<script setup>
import { onMounted, onUnmounted, ref } from "vue";
import AMapLoader from "@amap/amap-jsapi-loader";

// 使用 ref 创建响应式数据存储经纬度
const latitude = ref(39.90923);
const longitude = ref(116.397428);

let map = null;


const props = defineProps({
  latitude :  12.2,
  longitude : 20.0,


})

onMounted(() => {
  AMapLoader.load({
    key: "00fa52820d61c178fb71a42f750d30f1",
    version: "2.0",
    plugins: [],
  })
      .then((AMap) => {
        map = new AMap.Map("container", {
          viewMode: "3D",
          zoom: 11,
          // 将经纬度绑定到地图的中心点位置
          center: [longitude.value, latitude.value],
        });
      })
      .catch((e) => {
        console.log(e);
      });

});

onUnmounted(() => {
  map?.destroy();
});

</script>

<style scoped>
#container {
  padding: 0px;
  margin: 0px;
  width: 100%;
  height: 500px;
}
</style>
