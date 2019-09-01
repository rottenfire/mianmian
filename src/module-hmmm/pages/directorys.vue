<template>
  <div class="dashboard-container">
    <div class="app-container">
      <!-- x新建 返回 标签 -->
      <div>
        <el-button type="primary" @click="add">新建目录</el-button>
        <el-button type="primary">返回学科</el-button>
      </div>
      <!-- 搜索 -->
      <div class="search">
        <span>标签名称</span>
        <el-input class="input" placeholder="请输入内容" v-model="input" clearable></el-input>
      </div>
      <!-- 内容表     表格标签 -->
      <div>
        <el-table :data="tableData" border style="width: 100%;">
          <el-table-column prop="id" label="序号" width="60"></el-table-column>
          <el-table-column prop="directoryName" label="标签名称" width="180"></el-table-column>
          <el-table-column prop="username" label="创建者"></el-table-column>
          <el-table-column prop="addDate" label="创建日期"></el-table-column>
          <el-table-column prop="totals" label="面试题数量"></el-table-column>
          <el-table-column prop="state" label="状态"></el-table-column>
          <el-table-column label="操作" width="200">
            <template slot-scope="stData">
              <el-button type="text" @click="revisionDirectorys(stData.row)">修改</el-button>
              <el-button type="text" @click="bearDirectorys(stData.row)">禁用</el-button>
              <el-button type="text" @click="delDirectorys(stData.row)">删除</el-button>
            </template>
          </el-table-column>
        </el-table>
      </div>
      <!-- 分页 -->
      <el-pagination
        class="pager"
        background
        @size-change="handleSizeChange"
        @current-change="handleCurrentChange"
        :current-page="page.page"
        :page-sizes="[10, 20, 30, 40]"
        :page-size="page.pageSize"
        layout="prev, pager, next,sizes,jumper"
        :total="page.total"
      ></el-pagination>
    </div>
    <template v-if="dialogVisible">
    <el-dialog title="新建目录" :visible.sync="dialogVisible" width="40%">
      <directorys-add @confirm="establish" @cancle="close" @edit="edit" :repair="repair"></directorys-add>
    </el-dialog>
    </template>
  </div>
</template>

<script>
// 导入新建目录组件
import addCatalogue from '../components/directorys-add'
// 按需导入 学科的api模块
import { list, remove, update, removeState } from '@/api/hmmm/directorys'
export default {
  created() {
    // 页面加载完毕立即获取学科数据
    this.getMaterial()
    console.log(this.repair)
    
  },
  name: 'DirectorysList',
  data() {
    return {
      repair: {},
      dialogVisible: false,
      tableData: [],
      input: '',
      page: {
        total: 0,
        page: 1,
        pageSize: 10
      }
    }
  },
  methods: {
    // 新增
    add() {
       this.dialogVisible = true
       this.repair = {}
    },
    // 修改
    revisionDirectorys(row) {
     this.dialogVisible = true
      // update({ id: row.id })
      this.repair = row
      // console.log(row)
    },
    // 禁止
    bearDirectorys(id) {},
    // 删除
    async delDirectorys(id) {
      await this.$confirm('是否删除')
      await remove(id)
      // console.log(id)
      this.getMaterial()
    },
    getMaterial() {
      list({ page: this.page.page }).then(result => {
        // console.log(result.data)
        this.tableData = result.data.items
        this.page.total = result.data.counts
      })
    },
    handleSizeChange(val) {
      this.getMaterial()
    },
    handleCurrentChange(val) {
      // 切换页码
      // console.log(val)
      this.page.page = Number(val)
      this.getMaterial()
    },

    establish(val, val2) {
      this.dialogVisible = val
      // var subjectInfo = await list()
      // this.tableData = subjectInfo.data.items
      // this.page.total = subjectInfo.data.counts
      // this.page.page =+++ subjectInfo.data.page
      // console.log(val2)
      this.page.page = Number(val2)
      this.getMaterial()
    },
    close(val) {
      this.dialogVisible = val
    },
    edit(val) {
      this.dialogVisible = val
       this.getMaterial()
    }
  },
  components: {
    'directorys-add': addCatalogue
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
