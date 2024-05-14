<script setup>

import axios from 'axios';
import {useRouter} from "vue-router";
import {onMounted} from "vue";
const router = useRouter()





const Login = async () => {

  console.log("准备登录")

  try {
       console.log(username.value)

    const formData = new FormData();
    formData.append('username', username.value);
    formData.append('pass', password.value);

      const response = await axios.post('http://localhost:8080/api/login',
        formData
      );


        if(response.data.status == 200){

          console.log("登录成功")
          localStorage.setItem('Token',response.data.data);
          console.log('Token:', response.data.data);
          router.push('/' ) // 根据路由名称跳转到登录页面


        }
        // 登录成功，进行相关操作，例如跳转到主页
   } catch (error) {
        // 登录失败，显示错误消息
        console.error('登录失败:', error);
        errorMessage.value = '用户名或密码错误，请重试。';
}








}


onMounted(()=>{


  console.log("检查");
  if(localStorage.getItem("Token")){


    router.push('/' ) // 根据路由名称跳转到登录页面


  }








})



</script>

<template>
  <div class="login-container">
    <h2 style="text-align: center">请先完成登录</h2>
    <form @submit.prevent="login">
      <div class="form-group">
        <label for="username">用户名:</label>
        <input type="text" id="username" v-model="username" required>
      </div>
      <div class="form-group">
        <label for="password">密码:</label>
        <input type="password" id="password" v-model="password" required>
      </div>
      <button @click="Login" type="submit">登录</button>
    </form>
  </div>
</template>

<style scoped>
.login-container {
  max-width: 400px;
  margin: 0 auto;
  padding: 20px;
  border-radius: 5px;
}

.form-group {
  margin-bottom: 20px;
}

label {
  display: block;
  margin-bottom: 5px;
}

input[type="text"],
input[type="password"] {
  width: 100%;
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 5px;
  box-sizing: border-box;
}

button[type="submit"] {
  display: block;
  width: 100%;
  padding: 10px;
  border: none;
  border-radius: 5px;
  background-color: #e19f59;
  color: #fff;
  cursor: pointer;
}
</style>