<template>
  <div class="dashboard-container">
    <div class="app-container">
      <el-row class="el_row">
        <el-button type="info" @click="toAdd">新增面试技巧</el-button>
      </el-row>
      <el-row type="flex" justify="space-between" class="el_row">
        <el-col :span="6">
          <el-input v-model="page.keyword" @change="getArticleList" placeholder="请输入题目编号/题干"></el-input>
        </el-col>
        <el-col :span="4">
          <el-button size="small" @click="page.keyword=''">清除</el-button>
          <el-button type="primary" size="small" @click="getArticleList()">搜索</el-button>
        </el-col>
      </el-row>
      <el-row class="el_row">
        <el-table :data="articleList" style="width: 100%" height="300">
          <el-table-column prop="id" label="序号" width="100" align="center"></el-table-column>
          <el-table-column prop="title" label="标题" width="338" align="center"></el-table-column>
          <el-table-column prop="reads" label="阅读数" width="120" align="center"></el-table-column>
          <el-table-column prop="state" label="状态" :formatter="format" width="120" align="center"></el-table-column>
          <el-table-column prop="creator" label="录入人" width="150" align="center"></el-table-column>
          <el-table-column label="操作" width="300" align="center">
            <template slot-scope="scope">
              <el-popover placement="right" width="500" trigger="click">
                <p>{{ articleDetail.title }}</p>
                <hr />
                <p v-html="articleDetail.articleBody"></p>
                <el-button
                  type="text"
                  size="small"
                  slot="reference"
                  @click.native.prevent="previewRow(scope.row)"
                >预览</el-button>
              </el-popover>
              <el-button
                @click.native.prevent="editRow(scope.row)"
                type="text"
                size="small"
              >修改</el-button>
              <el-button @click.native.prevent="deleteRow(scope.row)" type="text" size="small">删除</el-button>
              <el-button
                @click.prevent="changeState(scope.row)"
                type="text"
                size="small"
              >{{scope.row.state ? '禁用' : '启用'}}</el-button>
            </template>
          </el-table-column>
        </el-table>
      </el-row>
      <el-row type="flex" justify="center" class="el_row">
        <el-pagination
          @size-change="handleSizeChange"
          @current-change="handleCurrentChange"
          :current-page="page.page"
          :page-sizes="[10, 20]"
          :page-size="10"
          layout="total, sizes, prev, pager, next, jumper"
          :total="totalPage"
        ></el-pagination>
      </el-row>
    </div>
  </div>
</template>

<script>
import { list, remove, detail, state } from '@/api/hmmm/articles'
export default {
  name: 'ArticlesList',
  data() {
    return {
      articleList: [],
      page: {
        page: 1,
        pagesize: 10,
        keyword: ''
      },
      totalPage: 0,
      articleDetail: {
        title: '0',
        articleBody: '0'
      }
    }
  },
  methods: {
    // 获取内容列表
    async getArticleList() {
      let result = await list(this.page)
      this.articleList = result.data.items
      this.totalPage = result.data.counts
    },
    // 每页显示条数变化时出发此函数
    handleSizeChange(val) {
      this.page.pagesize = val
      this.getArticleList()

    },
    // 当前页码发生变化时出发此函数
    handleCurrentChange(val) {
      this.page.page = val
      this.getArticleList()

    },
    // 删除行数据
    deleteRow(row) {
      this.$confirm('确定删除？', '提示').then(() => {
        remove(row)
        this.getArticleList()
        this.$message({
          type: 'success',
          message: '删除成功!'
        })
      })
    },
    // 文章预览，文章详情
    async previewRow(row) {
      let result = await detail(row)
      this.articleDetail = result.data
    },
    format(row, column, cellValue, index) {
      return row.state ? '启用' : '禁用'
    },
    // 改变显示状态
    async changeState(row) {
      // row.state = Number(!row.state)
      row.state = row.state ? 0 : 1
      let result = await state(row) // 后端未作相应处理，页面数据没有改变
      this.getArticleList()
    },
    toAdd() {
      this.$router.push(`/articles/add/${' '}`)
    },
    editRow(row) {
      this.$router.push(`/articles/add/${row.id}`)

    }
  },
  created() {
    this.getArticleList()
  }
}
</script>

<style scoped>
.el-row {
  margin-bottom: 10px;
}
</style>
