<template>
  <div class="dashboard-container">
    <div class="app-container">
      <!-- 新建学科弹出框 -->
      <el-form ref="formData" :rules="rules" :model="formData" label-width="100px">
        <el-form-item prop="subjectName" label="学科名称">
          <el-input v-model="formData.subjectName" style="width:500px;" placeholder="请输入"></el-input>
        </el-form-item>
        <el-form-item prop="isFrontDisplay">
          <el-switch v-model="formData.isFrontDisplay" inactive-text="是否显示"></el-switch>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="close">取消</el-button>
        <el-button type="primary" @click="addSubject(formData)">确认</el-button>
      </div>
    </div>
  </div>
</template>

<script>
// 引入学科添加
import { add } from '@/api/hmmm/subjects'
export default {
  name: 'SubjectsAdd',
  data() {
    return {
      dialogTableVisible: true,
      formData: {
        subjectName: '',
        isFrontDisplay: false
      },
      rules: {
        subjectName: [{ required: true, message: '学科名称不能为空' }]
      }
    }
  },
  methods: {
    // 关闭弹窗
    close() {
      return this.$emit('closedialog', false)
    },
    // 添加新的学科分类
    addSubject(formData) {
      this.$refs.formData.validate(async isOk => {
        if (isOk) {
          let result = await add(formData)
          // 添加成功清空输入框内容，并关闭弹窗
          if (result.status === 200) {
            this.$refs.formData.resetFields()
            this.close()
          }
        }
      })
    }
  }
}
</script>

<style scoped>
</style>
