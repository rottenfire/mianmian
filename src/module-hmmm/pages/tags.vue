<template>
  <div class="dashboard-container">
    <div class="app-container">学科管理 > 标签管理</div>
    <!-- form表单组件 --------------------------------------------------------- -->
    <el-form :model="formData">
      <el-form-item style="margin-left:5px">
        <el-button type="primary" @click="addTable">新建标签</el-button>
        <el-button type="primary" @click="goSubject">返回学科</el-button>
      </el-form-item>
      <el-form-item v-model="formData.labelName" label="标签名称:" label-width="100px" style="width:300px">
        <el-input placeholder="请输入"></el-input>
      </el-form-item>
    </el-form>
    <!-- table表格组件 ------------------------------------------------------- -->
    <el-table :data="tableData" border style="width: 100%">
      <el-table-column prop="id" label="序号" width="60"></el-table-column>
      <el-table-column prop="tagName" label="标签名称" width="300"></el-table-column>
      <el-table-column prop="creator" label="创建者" width="100"></el-table-column>
      <el-table-column prop="addDate" label="日期" width="200">
        <span
          slot-scope="stData"
        >{{ stData.row.addDate | parseTimeByString('{y}-{m}-{d} {h}:{i}:{s}') }}</span>
      </el-table-column>
      <el-table-column prop="totals" label="面试题数量" width="100"></el-table-column>
      <el-table-column prop="state" label="状态" width="100" :formatter="formatter"></el-table-column>
      <el-table-column label="操作">
        <template slot-scope="stData">
          <el-button type="text" @click="editTable(stData.row)">修改</el-button>
          <el-button type="text" @click="ProhibitTable(stData.row)">禁用</el-button>
          <el-button type="text" @click="deleteTable(stData.row)">删除</el-button>
        </template>
      </el-table-column>
    </el-table>
    <!-- 分页组件 ------------------------------------------------------------- -->
    <el-row type="flex" justify="center" style="margin:20px 0">
      <el-pagination
        :current-page="page.page"
        :page-size="page.pageSize"
        :total="page.pages"
        @current-change="changePage"
        background
        layout="prev, pager, next"
      ></el-pagination>
    </el-row>
    <!-- 新增标签 -------------------------------------------------------------- -->
    <template v-if="tagsAddTableVisible">
      <el-dialog title="新增标签" :visible.sync="tagsAddTableVisible">
        <tags-add :addEdit="addEdit" @closedialog="closedialog"></tags-add>
      </el-dialog>
    </template>
  </div>
</template>

<script>
import { list, add, remove, update } from '@/api/hmmm/tags'
import tagsAdd from '../components/tags-add'

export default {
  name: 'TagsList',
  data() {
    return {
      // 新增标签弹窗 默认不显示
      tagsAddTableVisible: false,
      // 表格列表
      tableData: [],
      // 搜索输入定义formData
      formData: {
        labelName: ''
      },
      // 分页
      page: {
        page: 1, // 当前
        pageSize: 10, // 每页多少条
        pages: 0// 总条数
      },
      // 新增/修改
      addEdit: {}
    }
  },
  // 方法
  methods: {
    // 获取列表信息 -----------------------------------
    async getTableData() {
      let params = {
        page: this.page.page,
        pageSize: this.page.pageSize
      }
      // 获取列表
      var getTableData = await list(params)
      // 获取列表信息赋值给data中 列表tableData
      this.tableData = getTableData.data.items
      // 获取总页数赋值给page.pages
      this.page.pages = getTableData.data.counts
      console.log(getTableData.data.counts)
    },
    // 添加标签 ----------------------------------------
    addTable() {
      // 点击添加 显示弹窗
      this.tagsAddTableVisible = true
      // 
      this.addEdit = {} // 问题
    },
    // 修改 --------------------------------------------
    editTable(row) {
      // 点击添加 显示弹窗
      this.tagsAddTableVisible = true
      //
      this.addEdit = row // 问题
      this.$emit('modifyTable', row)
    },
    // 关闭弹窗 ----------------------------------------
    closedialog(close) {
      this.tagsAddTableVisible = close
      // 重新获取
      this.getTableData()
    },
    // 删除 --------------------------------------------
    async deleteTable(id) {
      await this.$confirm('删除么？', '提示')
      await remove(id)
        // console.log(id)
        // 重新获取数据
      this.getTableData()
    },
    // 点击跳转学科 ------------------------------------
    goSubject() {
      this.$router.push('list')
    },
    // 点击页码实现跳转当前点击页 -----------------------
    changePage(newPage) {
      this.page.page = newPage
      // 重新获取
      this.getTableData()
    },
    // 状态 -------------------------------------------
    // formatter 需要返回结果 需要根据当前值进行返回
    // formatter 是elementUI提供的过滤器是Vue自特性
    // row 当条数据对象
    // cilumn 当前列的属性
    // cellValue 当前单元格的属性
    // index 索引
    formatter(row, column, cellValue, index) {
      return cellValue ? '启用' : '屏蔽'
    },
    // 禁用 -------------------------------------------
    ProhibitTable(row) {}
  },

  // 渲染
  created() {
    this.getTableData()
  },

  // 局部组件  弹窗显示
  components: {
    // 注册标签添加组件
    'tags-add': tagsAdd
  }
}
</script>

<style lang="less" scoped>
.dashboard-container {
  margin: 0 30px;
}
</style>
