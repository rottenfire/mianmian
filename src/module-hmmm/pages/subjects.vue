<template>
  <div class="dashboard-container">
    <div class="app-container">
      <el-card class="manage-content">
        <el-row class="add-btn" style="margin-bottom:30px;">
          <el-button @click="addItem">新增学科</el-button>
        </el-row>
        <!-- 搜索按钮区域 -->
        <el-form :model="formData" label-width="80px" class="manage-search">
          <el-row type="flex" justify="space-between">
            <el-form-item label="学科管理">
              <el-input
                @input="changeEvent"
                v-model="formData.subjectName"
                placeholder="请输入"
                style="width:200px"
              ></el-input>
            </el-form-item>
            <div>
              <el-button @click="clearInputValue">清除</el-button>
              <el-button @click="getArticlesList(conditions())">搜索</el-button>
            </div>
          </el-row>
        </el-form>
        <!-- 内容显示区域 -->
        <el-table :data="items">
          <el-table-column align="center" prop="id" width="80" label="序号"></el-table-column>
          <el-table-column align="center" prop="subjectName" width label="学科名称"></el-table-column>
          <el-table-column align="center" prop="username" width label="创建者"></el-table-column>
          <el-table-column align="center" width label="创建日期">
            <template slot-scope="time">{{time.row.addDate | parseTimeByString(time.row.addDate)}}</template>
          </el-table-column>
          <el-table-column align="center" prop="isFrontDisplay" width="120" label="前台是否显示"></el-table-column>
          <el-table-column align="center" prop="twoLevelDirectory" width="80" label="二级目录"></el-table-column>
          <el-table-column align="center" prop="tags" width="80" label="标签"></el-table-column>
          <el-table-column align="center" prop="totals" width="80" label="题目数量"></el-table-column>
          <el-table-column align="center" width="300" label="操作">
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
      // 条件搜索相关数据
      formData: {
        subjectName: ''
      },
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
          this.$message({ message: '删除成功', type: 'success' })
          //  刷新列表
          this.getArticlesList(this.conditions())
        }
      })
    },
    // 关闭弹出框
    closeDialog(close) {
      this.dialogTableVisible = close
      this.getArticlesList(this.conditions())
    },
    // 当前页改变
    handleCurrentChange(newPage) {
      this.page.currentPage = newPage
      this.getArticlesList(this.conditions())
    },
    // 每页显示的条数改变
    handleSizeChange(newPageSize) {
      this.page.pageSize = newPageSize
      this.getArticlesList(this.conditions())
    },
    // 输入框内容改变触发事件
    changeEvent() {
      this.getArticlesList(this.conditions())
    },
    // 清除输入框内容触发事件
    clearInputValue() {
      this.formData.subjectName = ''
      this.getArticlesList(this.conditions())
    },
    // 学科获取条件
    conditions() {
      let { subjectName } = this.formData
      let params = {
        subjectName
      }
      params.page = this.page.currentPage
      params.pagesize = this.page.pageSize

      return params
    },

    // 获取学科列表中的数据
    async getArticlesList(params) {
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
