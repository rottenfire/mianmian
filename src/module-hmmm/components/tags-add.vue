<template>
  <div class="dashboard-container">
    <div class="app-container">
      <el-form :model="formData" ref="form" :rules="rules">
        <el-form-item label="标签名称:" label-width="100px" prop="tagName">
          <el-input v-model="formData.tagName" style="width:400px"></el-input>
        </el-form-item>
      </el-form>
      <div class="tagsAddFooter">
        <el-button type="primary" @click="addTable(formData)">确定</el-button>
        <el-button @click="close">取消</el-button>
      </div>
    </div>
  </div>
</template>

<script>
import {add, update} from '../../api/hmmm/tags'
export default {
  name: 'TagsAdd',
  // props: ['modify'],
  data() {
    return {
      formData: {
        tagName: '',
        subjectID: 123
      },
      rules: {
        tagName: [{ required: true, message: '标签名称不能为空' }]
      }
    }
  },
  methods: {
    // 获取修改标签信息 modifyTable----------------------------
    // async editItem() {
    //  await update()
    // },
    modifyTable(row) {
      // this.$on('modifyTable')
      this.formData.tagName = this.modify.tagName
      this.formData.subjectID = this.modify.subjectID
      console.log(this.modify.tagName)
    },
    // 添加标签 ------------------------------------
    addTable(formData) {
      this.$refs.form.validate(async isOK => {
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
  },
  created() {
    // this.modifyTable()
  }
}
</script>

<style scoped>
.tagsAddFooter {
  float: right;
}
</style>
