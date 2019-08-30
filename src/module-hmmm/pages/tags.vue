<template>
  <div class="dashboard-container">
    <div class="app-container">学科管理 > 标签管理</div>
    <!-- f          ---------------------------------------------------------- -->
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
      <el-table-column prop="addDate" label="日期" width="200">
        <span slot-scope="stData">{{ stData.row.addDate | parseTimeByString('{y}-{m}-{d} {h}:{i}:{s}') }}</span>
      </el-table-column>
      <el-table-column prop="totals" label="面试题数量" width="100"></el-table-column>
      <el-table-column prop="state" label="状态" width="100" :formatter="formatter"></el-table-column>
      <el-table-column label="操作">
        <template slot-scope="stData">
          <el-button type="text">修改</el-button>
          <el-button type="text">禁用</el-button>
          <el-button type="text" @click="deleteTable(stData.row)">删除</el-button>
        </template>
      </el-table-column>
    </el-table>
    <!-- 分页组件-------------------------------------------------------------- -->
    <el-row type="flex" justify="center" style="margin:20px 0">
      <el-pagination
        :current-page="page.currentPage"
        :page-size="page.pageSize"
        :total="page.total"
        background
        layout="prev, pager, next"
      ></el-pagination>
    </el-row>    
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
      },
      page: {
        total: 5,
        pageSize: 1,
        currentPage: 1
      }
    }
  },
  methods: {
    // 获取列表信息 -----------------------------------
    async getTableData() {
      // 获取列表
      var getTableData = await list()
      // console.log(getTableData.data.items)
      this.tableData = getTableData.data.items
    },
    // 添加跳转 ----------------------------------------
    getTableDataAdd() {
      // 点击新建跳转添加页面
      this.$router.push('/subjects/tags/tagsAdd')
    },
    // 删除 --------------------------------------------
    async deleteTable(id) {
      await this.$confirm('删除么？')
      remove(id).then(() => {
        // console.log(id)
        // 重新获取数据
        this.getTableData()
      })
    },
    // 状态 -------------------------------------------
    // formatter 需要返回结果 需要根据当前值进行返回
    // formatter 是elementUI提供的过滤器是Vue自特性
    // row 当条数据对象
    // cilumn 当前列的属性
    // cellValue 当前单元格的属性
    // index 索引
    formatter (row, column, cellValue, index) {
      return cellValue ? '启用' : '屏蔽'
    }
    // -----------------------------------------------
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
