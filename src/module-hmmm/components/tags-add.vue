<template>
  <div class="dashboard-container">
    <div class="app-container">
      <el-form :model="formData" ref="form" :rules="rules">
        <el-form-item label="标签名称:" label-width="100px" prop="label">
          <el-input v-model="formData.label" style="width:400px"></el-input>
        </el-form-item>
      </el-form>
      <div class="tagsAddFooter">
        <el-button type="primary" @click="modifyTable(formData)">确定</el-button>
        <el-button type="primary" @click="close">取消</el-button>
      </div>
    </div>
  </div>
</template>

<script>
import {add, update} from '../../api/hmmm/tags'
export default {
  name: 'TagsAdd',
  data() {
    return {
      formData: {
        label: ''
      },
      rules: {
        label: [{ required: true, message: '标签名称不能为空' }]
      }
    }
  },
  methods: {
    // 修改标签 ------------------------------------
    // 添加标签 ------------------------------------
    modifyTable(formData) {
      this.$refs.formData.validate(async isOK => {
        if (isOK) {
          let result = await add(formData)
          // 添加成功清空输入框内容并关闭弹窗
          if (result.status === 200) {
            this.$refs.formData.resetFields()
            this.close()
          }
        }
      })
    },
    // 点击取消关闭弹窗 -----------------------------
    close() {
      this.$emit('closedialog', false)
    }
  }
}
</script>

<style scoped>
.tagsAddFooter {
  float: right;
}
</style>
