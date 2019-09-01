<template>
  <div class="dashboard-container">
    <div class="app-container">
      <el-form :model="formData" ref="form" :rules="rules">
        <el-form-item label="标签名称:" label-width="100px" prop="tagName">
          <el-input v-model="formData.tagName"></el-input>
        </el-form-item>
      </el-form>
      <div class="tagsAddFooter">
        <el-button type="primary" @click="addTable">确定</el-button>
        <el-button @click="close">取消</el-button>
      </div>
    </div>
  </div>
</template>

<script>
import {add, detail} from '@/api/hmmm/tags'
export default {
  name: 'TagsAdd',
  props: ['addEdit'],
  data() {
    return {
      formData: {
        id: null,
        tagName: '',
        subjectID: 123
      },
      rules: {
        tagName: [{ required: true, message: '标签名称不能为空' }]
      }
    }
  },
  methods: {
    // 添加标签 ------------------------------------
     addTable() {
      this.$refs.form.validate(isOK => {
        if (isOK) {
          let result = add(this.formData)
          // 添加成功关闭弹窗
          this.$emit('closedialog', false)
        }
      })
    },
    // 点击取消关闭弹窗 -----------------------------
    close() {
      this.$emit('closedialog', false)
    }
  },
 async created() { 
    let {id} = this.addEdit
    this.formData = id ? (await detail({ id })).data : this.formData
  }
}
</script>

<style scoped>
.tagsAddFooter {
  float: right;
}
</style>
