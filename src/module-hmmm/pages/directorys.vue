<template>
  <div class="dashboard-container">
    <div class="app-container">
      <!-- x新建 返回 标签 -->
      <div>
        <el-button type="primary">新建目录</el-button>
        <el-button type="primary">返回学科</el-button>
      </div>
      <!-- 搜索 -->
      <div class="search">
        <span>标签名称</span>
        <el-input class="input" placeholder="请输入内容" v-model="input" clearable></el-input>
      </div>
      <!-- 内容表     表格标签 -->
      <div>
        <el-table :data="tableData" border style="width: 100%">
          <el-table-column prop="date" label="序号" width="60"></el-table-column>
          <el-table-column prop="name" label="标签名称" width="180"></el-table-column>
          <el-table-column prop="address" label="创建者"></el-table-column>
          <el-table-column prop="address" label="创建日期"></el-table-column>
          <el-table-column prop="address" label="面试题数量"></el-table-column>
          <el-table-column prop="address" label="状态"></el-table-column>
          <el-table-column label="操作" width="200">
            <el-button type="text">修改</el-button>
            <el-button type="text">禁用</el-button>
            <el-button type="text">删除</el-button>
          </el-table-column>
        </el-table>
      </div>
      <!-- 分页 -->
      <el-pagination
        class="pager"
        background
        @size-change="handleSizeChange"
        @current-change="handleCurrentChange"
        :current-page.sync="currentPage1"
        :page-sizes="[10, 20, 30, 40]"
        :page-size="10"
        layout="prev, pager, next,sizes,jumper"
        :total="1000"
      ></el-pagination>
    </div>
  </div>
</template>

<script>
export default {
  name: 'DirectorysList',
  data() {
    return {
      tableData: [{}],
      input: ''
    }
  },
  methods: {
      handleSizeChange(val) {
        console.log(`每页 ${val} 条`)
      },
      handleCurrentChange(val) {
        console.log(`当前页: ${val}`)
      }
  }
}
</script>

<style lang="less" scoped>
.search {
  background-color: #ccc;
  width: 260px;
  height: 36px;
  font-size: 14px;
  margin-top: 10px;
  .input {
    width: 200px;
  }
}
.pager {
  text-align: center;
  margin-top: 10px;
}
</style>
