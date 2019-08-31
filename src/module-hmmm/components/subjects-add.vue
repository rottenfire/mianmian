<template>
  <div class="dashboard-container">
    <div class="app-container">
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
        <el-button type="primary" @click="edit.id ? editSubject(formData):addSubject(formData)">确认</el-button>
      </div>
    </div>
  </div>
</template>

<script>
// 引入学科添加
import { add, update } from '@/api/hmmm/subjects'
export default {
  name: 'SubjectsAdd',
  props: ['edit'],
  data() {
    return {
      dialogTableVisible: true,
      formData: {
        subjectName: '',
        isFrontDisplay: false,
        id: null
      },
      // 校验规则
      rules: {
        subjectName: [{ required: true, message: '学科名称不能为空' }]
      }
    }
  },
  methods: {
    // 修改
    editSubject(formData) {
      this.$refs.formData.validate(async isOk => {
        if (isOk) {
          let result = await update(formData)
          if (result.status === 200) {
            this.$message({ message: '修改成功', type: 'success' })
            this.close()
          } else {
            this.$message({ message: result.statusText, type: 'warning' })
          }
        }
      })
    },
    // 编辑时获取当前修改学科的信息
    getAddSubject() {
      this.formData.subjectName = this.edit.subjectName
      this.formData.isFrontDisplay = !!this.edit.isFrontDisplay
      this.formData.id = this.edit.id
    },
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
            this.$message({ message: '添加成功', type: 'success' })
            this.close()
          }
        }
      })
    }
  },
  created() {
    this.getAddSubject()
  }
}
</script>

<style scoped>
</style>
