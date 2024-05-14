<script setup>
import {useRouter} from "vue-router";
import {onMounted, ref, watchEffect} from "vue";
const router = useRouter()


const hasLocalData = ref(false);

onMounted(()=>{

  console.log("首次渲染时调用")

  const data = localStorage.getItem('Token');
  if (data) {
    hasLocalData.value = true;
  }else {
    hasLocalData.value = false;

  }


})

watchEffect(()=>{

    console.log("界面变动时调用")

    const data = localStorage.getItem('Token');
    if (data) {
      hasLocalData.value = true;
    }else {
      hasLocalData.value = false;

    }



})


////  当点击退出登录时 删除Token 退回到登陆界面

const Login_out = () => {

  localStorage.removeItem("Token")
  router.push('/' ) // 根据路由名称跳转到登录页面
  window.location.reload()



}




</script>

<template >

  <div style="  width: 100%; height: 100%">

    <el-row  style="align-items: center ; width: 100%; height: 100%">
      <el-col :span="4" style="text-align: center">
        <h3 style="color: antiquewhite ;display: inline ;float: left">Gfly 控制台</h3>
      </el-col>
      <el-col :span="4">
      </el-col>
      <el-col :span="4"><div class="grid-content ep-bg-purple" /></el-col>
      <el-col :span="4"><div class="grid-content ep-bg-purple-light" /></el-col>
      <el-col :span="4"><div class="grid-content ep-bg-purple" /></el-col>
      <el-col :span="4"  v-if="hasLocalData" >



        <el-popover :visible="visible" placement="top" :width="160">
          <div style="text-align: center ; margin: 0">
            <el-button  type="primary" @click="Login_out">
              退出登录
            </el-button>
          </div>
          <template #reference>

              <el-avatar  style="float: right"  src="https://cube.elemecdn.com/0/88/03b0d39583f48206768a7534e55bcpng.png"/>

          </template>
        </el-popover>


      </el-col>
    </el-row>



  </div>




</template>

<style scoped>

</style>