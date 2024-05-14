<template>

    <img style="max-width: 100%" ref="videoFrame" />

</template>

<script setup>
import { ref, onMounted } from 'vue';

const videoFrame = ref(null);

onMounted(() => {
  const ws = new WebSocket('ws://192.168.31.80:8765'); // 你的服务器地址和端口

  ws.binaryType = 'arraybuffer'; // 设置数据类型为二进制数组

  ws.onopen = () => {
    console.log('WebSocket 连接已建立');
  };

  ws.onmessage = (event) => {
    const arrayBuffer = event.data;

    // 解析帧大小（前四个字节）
    const sizeBuffer = new Uint8Array(arrayBuffer, 0, 4);
    const dataSize = sizeBuffer.reduce((acc, byte, index) => acc + byte * Math.pow(256, 3 - index), 0);

    //console.log("size" + dataSize)
    // 提取帧数据
    const frameData = arrayBuffer.slice(4);

    // 将帧数据转换为 Blob 对象
    const blob = new Blob([frameData], { type: 'image/jpeg' });

    // 创建 URL 对象
    const imageUrl = URL.createObjectURL(blob);

    // 更新图像元素的 src
    videoFrame.value.src = imageUrl;
  };

  ws.onerror = (error) => {
    console.error('WebSocket 出错:', error);
  };

  ws.onclose = () => {
    console.log('WebSocket 连接已关闭');
  };
});
</script>

<style scoped>
/* 可以添加样式 */
</style>
