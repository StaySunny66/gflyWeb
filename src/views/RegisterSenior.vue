<template>




  <el-row class="row-bg" style="margin-top: 10px" >

    <el-col :span="16"   style="padding-left: 50px">
      <el-text style="font-size: 30px;color: #E76F51">传感器注册</el-text>
    </el-col>
  </el-row>
  <el-row class="row-bg" justify="center" style="margin-top: 20px">

    <el-col :span="14"    style="padding-left: 50px;padding-right: 30px">


      <el-form :model="form" label-width="auto" size="default"
       label-position="top" style="max-width: 500px"
      >

        <el-text style="display: block;font-size: 20px;color: #E76F51">传感器名称</el-text>

        <el-form-item label="传感器名称用于表示注册传感器的在系统中的名称">

          <el-input v-model="form.name" />
        </el-form-item>


        <el-text style="display: block;font-size: 20px;color: #E76F51">地图可视化</el-text>

        <el-form-item label="开启地图可视化功能后可结合热力图功能实现传感器数据和地图的融合">
          <el-switch v-model="form.seniorMapEn" />
        </el-form-item>

        <el-text style="display: block;font-size: 20px;color: #E76F51">Web端实时显示</el-text>
        <el-form-item label="开启后传感器数据可以在飞行主界面实时显示">
          <el-switch v-model="form.seniorViewEn" />
        </el-form-item>



        <el-form-item label="传感器备注消息">
          <el-input v-model="form.Others" type="textarea" />
        </el-form-item>


        <el-form-item>
          <el-button type="primary" @click="onSubmit">注册</el-button>
        </el-form-item>
      </el-form>




    </el-col>
    <el-col :span="10">

      <el-image src="http://g.shilight.cn/sideLogo.png" fit="none">


      </el-image >


    </el-col>


  </el-row>




</template>

<script lang="ts" setup>
import {reactive, ref} from 'vue'
import axios from 'axios';
import {ElMessage} from "element-plus";





// do not use same name with ref
const form = reactive({
  name: '',
  AutoEn: false,
  seniorMapEn: false,
  seniorViewEn: false,
  Others: '',
})

const onSubmit = async () => {
  console.log("开始添加传感器")

  try {
    console.log(form)

    const formData = new FormData();
    formData.append('name', form.name);
    formData.append('SeniorMapEn', form.seniorMapEn);
    formData.append('SeniorViewEn', form.seniorViewEn);
    formData.append('AutoEn', false);
    formData.append('Others', form.Others);


    const response = await axios.post('http://localhost:8080/api/newSenior',
        formData
    );


    if (response.data.status == 200) {

      console.log("添加成功")

        ElMessage({
          message: '恭喜添加成功',
          type: 'success',
        })

    }else {

      console.error('添加失败');
      ElMessage.error('添加失败，请检查输入内容')

    }
    // 登录成功，进行相关操作，例如跳转到主页
  } catch (error) {
    // 登录失败，显示错误消息
    console.error('添加失败:', error);
    ElMessage.error('添加失败',error)
  }
}






</script>
