<template>
  <div class="dashboard-container">
    <div class="app-container">
      <!-- 新建学科弹出框 -->
      <el-dialog title="新增学科" :visible.sync="dialogTableVisible">
        <el-form label-width="100px">
          <el-form-item label="学科名称">
            <el-input style="width:500px;" placeholder="请输入"></el-input>
          </el-form-item>
          <el-form-item>
            <el-switch inactive-text="是否显示"></el-switch>
          </el-form-item>
        </el-form>
        <div slot="footer">
          <el-button @click="dialogTableVisible = false">取消</el-button>
          <el-button type="primarys" @click="dialogTableVisible = false">确认</el-button>
        </div>
      </el-dialog>
    </div>
  </div>
</template>

<script>
export default {
  name: 'SubjectsAdd',
  props: ['flag'],
  data() {
    return {
            // 新增学科对话框弹出标记
      dialogTableVisible: flag
    }
  }
}
</script>

<style scoped>
</style>
