<template>
  <div class="dashboard-container">
    <div class="app-container">
      <el-row class="el_row">
        <el-button type="info">新增面试技巧</el-button>
      </el-row>
      <el-row type="flex" justify="space-between" class="el_row">
        <el-col :span="6">
          <el-input v-model="input" placeholder="请输入题目编号/题干"></el-input>
        </el-col>
        <el-col :span="4">
          <el-button size="small">清除</el-button>
          <el-button type="primary" size="small">搜索</el-button>
        </el-col>
      </el-row>
      <el-row class="el_row">
        <el-table :data="tableData" style="width: 100%" height="300">
          <el-table-column prop="date" label="序号" width="100" align="center"></el-table-column>
          <el-table-column prop="name" label="标题" width="338" align="center"></el-table-column>
          <el-table-column prop="province" label="阅读数" width="120" align="center"></el-table-column>
          <el-table-column prop="city" label="状态" width="120" align="center"></el-table-column>
          <el-table-column prop="address" label="录入人" width="150" align="center"></el-table-column>
          <el-table-column label="操作" width="300" align="center">
            <template slot-scope="scope">
              <el-button
                @click.native.prevent="deleteRow(scope.$index, tableData)"
                type="text"
                size="small"
              >预览</el-button>
              <el-button
                @click.native.prevent="deleteRow(scope.$index, tableData)"
                type="text"
                size="small"
              >修改</el-button>
              <el-button
                @click.native.prevent="deleteRow(scope.$index, tableData)"
                type="text"
                size="small"
              >删除</el-button>
              <el-button
                @click.native.prevent="deleteRow(scope.$index, tableData)"
                type="text"
                size="small"
              >禁用</el-button>
            </template>
          </el-table-column>
        </el-table>
      </el-row>
      <el-row type="flex" justify="center" class="el_row">
        <el-pagination
          @size-change="handleSizeChange"
          @current-change="handleCurrentChange"
          :current-page="currentPage4"
          :page-sizes="[100, 200, 300, 400]"
          :page-size="100"
          layout="total, sizes, prev, pager, next, jumper"
          :total="400"
        ></el-pagination>
      </el-row>
    </div>
  </div>
</template>

<script>
export default {
  name: 'ArticlesList',
  data() {
    return {
      searchWords: '',
      tableData: [
        {
          date: '2016-05-02',
          name: '王小虎',
          province: '上海',
          city: '普陀区',
          address: '上海市 弄',
          zip: 200333
        },
        {
          date: '2016-05-04',
          name: '王小虎',
          province: '上海',
          city: '普陀区',
          address: '上海路 1517 弄',
          zip: 200333
        },
        {
          date: '2016-05-01',
          name: '王小虎',
          province: '上海',
          city: '普陀区',
          address: '上路 1519 弄',
          zip: 200333
        },
        {
          date: '2016-05-03',
          name: '王小虎',
          province: '上海',
          city: '普陀区',
          address: '江路 1516 弄',
          zip: 200333
        }
      ]
    }
  }
}
</script>

<style scoped>
  .el-row {
    margin-bottom: 10px;
  }
</style>
