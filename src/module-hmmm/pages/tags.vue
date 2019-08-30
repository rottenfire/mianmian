<template>
  <div class="dashboard-container">
    <div class="app-container">学科管理 > 标签管理</div>
    <!-- form表单组件---------------------------------------------------------- -->
    <el-form :model="formData">
      <el-form-item style="margin-left:5px">
        <el-button type="primary" @click="getTableDataAdd">新建学科</el-button>
        <el-button type="primary">返回学科</el-button>
      </el-form-item>
      <el-form-item v-model="formData.label" label="标签名称:" label-width="100px" style="width:300px">
        <el-input placeholder="请输入"></el-input>
      </el-form-item>
    </el-form>
    <!-- table表格组件-------------------------------------------------------- -->
    <el-table :data="tableData" border style="width: 100%">
      <el-table-column prop="id" label="序号" width="60"></el-table-column>
      <el-table-column prop="tagName" label="标签名称" width="300"></el-table-column>
      <el-table-column prop="subjectName" label="创建者" width="100"></el-table-column>
      <el-table-column prop="addDate" label="日期" width="200"></el-table-column>
      <el-table-column prop="totals" label="面试题数量" width="100"></el-table-column>
      <el-table-column prop="state" label="状态" width="100"></el-table-column>
      <el-table-column label="操作">
        <el-button type="text">修改</el-button>
        <el-button type="text">禁用</el-button>
        <el-button type="text" @click="deleteTable(id)">删除</el-button>
      </el-table-column>
    </el-table>
  </div>
</template>

<script>
import { list, add, remove } from '@/api/hmmm/tags'

export default {
  name: 'TagsList',
  data() {
    return {
      tableData: [],
      formData: {
        label: ''
      }
    }
  },
  methods: {
    async getTableData() {
      // 获取列表
      var getTableData = await list()
      // console.log(getTableData.data.items)
      this.tableData = getTableData.data.items
    },
    getTableDataAdd() {
      // 点击新建跳转添加页面
      this.$router.push('/subjects/tags/tagsAdd')
    },
    async deleteTable(id) {
      await this.$confirm('删除么？')
      var deleteTable = await remove({id})
      console.log(deleteTable)
      // 重新获取数据
      this.getTableData()
    }
  },
  created() {
    this.getTableData()
  }
}
</script>

<style lang="less" scoped>
.dashboard-container {
  margin: 0 30px;
}
</style>
