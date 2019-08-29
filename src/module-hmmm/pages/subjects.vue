<template>
  <div class="dashboard-container">
    <div class="app-container">
      <el-card class="manage-content">
        <el-row class="add-btn" style="margin-bottom:30px;">
          <el-button>新增学科</el-button>
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
        <el-table :data="tableData">
          <el-table-column prop="id" width="80" label="序号"></el-table-column>
          <el-table-column prop width label="学科名称"></el-table-column>
          <el-table-column prop width label="创建者"></el-table-column>
          <el-table-column prop width label="创建日期"></el-table-column>
          <el-table-column prop width label="前台是否显示"></el-table-column>
          <el-table-column prop width label="二级目录"></el-table-column>
          <el-table-column prop width label="标签"></el-table-column>
          <el-table-column prop width label="题目数量"></el-table-column>
          <el-table-column prop width="300" label="操作"></el-table-column>
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
            :page-sizes="[100, 200, 300, 400]"
            :page-size="page.pageSize"
          ></el-pagination>
        </el-row>
      </el-card>
    </div>
  </div>
</template>

<script>
export default {
  name: 'SubjectsList',
  data() {
    return {
      tableData: [{ id: '1' }],
      // 分页数据
      page: {
        currentPage: 1,
        pageSize: 10,
        total: 0
      }
    }
  },
  methods: {
    // 当前页改变
    handleCurrentChange(newPage) {

    },
    // 每页显示的条数改变
    handleSizeChange() {

    }
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
