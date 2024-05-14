<script setup>

import {onMounted, reactive} from "vue";
import axios from 'axios';
import {ElMessage} from "element-plus";

let tableData = reactive([]);



// 界面数据获取
onMounted(async ()=>{

  try {
    const response = await axios.get('http://127.0.0.1:8080/api/getSeniors'); // 替换为你的API地址
    tableData.splice(0, tableData.length, ...response.data.data);
    console.log(response.data)
  } catch (error) {
    console.error('Error:', error);
  }


})


const handleDelete = async (index) => {
  console.log("删除"+tableData[index])

  const formData = new FormData();
  formData.append('id', tableData[index].seniorId);

  const response = await axios.post('http://localhost:8080/api/deleteSenior',
      formData
  );


  if (response.data.status == 200) {

    console.log("删除完成")

    ElMessage({
      message: '删除成功',
      type: 'success',
    })

    window.location.reload()


  }else {

    console.error('删除失败');
    ElMessage.error('删除失败，请检查网络连接')

  }



}


</script>

<template>
  <el-row class="row-bg" style="margin-top: 10px;padding: 10px" >
    <el-text style="font-size: 30px;color: #E76F51">传感器管理</el-text>
  </el-row>


  <el-row class="row-bg" style="margin-top: 10px;padding: 10px"  justify="center"  >
    <el-col :span="24">

      <el-table :data="tableData"  style="width: 100%; background: rgba(231,111,81,0.12);">
        <el-table-column prop="seniorName" label="传感器名称" width="180" />
        <el-table-column prop="autoEn" label="开机自启" width="100" />
        <el-table-column prop="seniorMapEn" label="地图显示" width="100" />
        <el-table-column prop="seniorViewEn" label="首页显示" width="100"/>

        <el-table-column label="操作">
          <template #default="scope">

            <el-button
                size="small"
                type="danger"
                @click="handleDelete(scope.$index)"
            >
              删除
            </el-button>
          </template>
        </el-table-column>
        <el-table-column prop="others" label="备注" />




      </el-table>




    </el-col>

  </el-row>




</template>

<style scoped>

</style>