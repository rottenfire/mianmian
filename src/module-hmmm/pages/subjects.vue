<template>
  <div class="dashboard-container">
    <div class="app-container">
      <el-card class="manage-content">
        <el-row class="add-btn" style="margin-bottom:30px;">
          <el-button @click="addItem">新增学科</el-button>
        </el-row>
        <!-- 搜索按钮区域 -->
        <el-form label-width="80px" class="manage-search">
          <el-form-item label="学科管理">
            <el-row type="flex" justify="space-between">
              <el-input placeholder="请输入" style="width:200px"></el-input>
              <div>
                <el-button>清除</el-button>
                <el-button>搜索</el-button>
              </div>
            </el-row>
          </el-form-item>
        </el-form>
        <!-- 内容显示区域 -->
        <el-table :data="items">
          <el-table-column prop="id" width="80" label="序号"></el-table-column>
          <el-table-column prop="subjectName" width label="学科名称"></el-table-column>
          <el-table-column prop="username" width label="创建者"></el-table-column>
          <el-table-column prop="addDate" width label="创建日期"></el-table-column>
          <el-table-column prop="isFrontDisplay" width="120" label="前台是否显示"></el-table-column>
          <el-table-column prop="twoLevelDirectory" width="80" label="二级目录"></el-table-column>
          <el-table-column prop="tags" width="80" label="标签"></el-table-column>
          <el-table-column prop="totals" width="80" label="题目数量"></el-table-column>
          <el-table-column width="300" label="操作">
            <template slot-scope="scoped">
              <el-link type="primary">学科分类</el-link>
              <el-link type="primary">学科标签</el-link>
              <el-link @click="editItem(scoped.row)" type="primary">修改</el-link>
              <el-link @click="delItem(scoped.row)" type="primary">删除</el-link>
            </template>
          </el-table-column>
        </el-table>
        <!-- 分页 -->
        <el-row class="pagination" type="flex" justify="center">
          <el-pagination
            background
            layout="total, sizes, prev, pager, next, jumper"
            :total="page.total"
            @size-change="handleSizeChange"
            @current-change="handleCurrentChange"
            :current-page="page.currentPage"
            :page-sizes="[10, 20, 30, 40]"
            :page-size="page.pageSize"
          ></el-pagination>
        </el-row>
        <template v-if="dialogTableVisible">
          <el-dialog title="新增学科" :visible.sync="dialogTableVisible">
            <!-- 新增学科组件 -->
            <subjects-add :edit="editInfo" @closedialog="closeDialog"></subjects-add>
          </el-dialog>
        </template>
      </el-card>
    </div>
  </div>
</template>

<script>
// 引入新增学科组件
import subjectsAdd from '@/module-hmmm/components/subjects-add'
// 引入学科列表,学科删除,学科修改
import { list, remove, update } from '@/api/hmmm/subjects'
export default {
  name: 'SubjectsList',
  data() {
    return {
      // 新增学科弹出框标志点
      dialogTableVisible: false,

      items: [],

      // 分页数据
      page: {
        currentPage: 1,
        pageSize: 10,
        total: 0
      },
      editInfo: {}
    }
  },
  methods: {
    // 学科新增
    addItem() {
      this.dialogTableVisible = true
      this.editInfo = {}
    },
    // 学科修改
    editItem(row) {
      this.dialogTableVisible = true
      this.editInfo = row
    },
    // 删除学科分类
    delItem(row) {
      this.$confirm('确认删除此条分类？', '提示').then(async () => {
        let result = await remove(row)
        if (result.status === 200) {
          this.$message({message: '删除成功', type: 'success'})
          this.getArticlesList()
          }
        //  刷新列表
        
      })
    },
    // 关闭弹出框
    closeDialog(close) {
      this.dialogTableVisible = close
      this.getArticlesList()
    },
    // 当前页改变
    handleCurrentChange(newPage) {
      this.page.currentPage = newPage
      this.getArticlesList()
    },
    // 每页显示的条数改变
    handleSizeChange(newPageSize) {
      this.page.pageSize = newPageSize
      this.getArticlesList()
    },
    // 获取学科列表中的数据
    async getArticlesList() {
      // 获取学科列表请求设置请求参数
      let params = { page: this.page.currentPage, pagesize: this.page.pageSize }

      var articlesList = await list(params)
      //  赋值给items
      this.items = articlesList.data.items
      //  总条数
      this.page.total = articlesList.data.counts
      // console.log(this.items)
    }
  },
  created() {
    this.getArticlesList()
  },
  components: {
    // 注册新增学科组件
    'subjects-add': subjectsAdd
  }
}
</script>

<style lang="less" scoped>
.dashboard-container {
  .app-container {
    .manage-content {
      .pagination {
        margin-top: 20px;
      }
      // .add-btn {
      //   margin-bottom: 10px;
      // }
    }
  }
}
</style>
