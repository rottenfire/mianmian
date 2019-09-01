<template>
  <div class="dashboard-container">
    <div class="app-container">
      <el-form ref="articleForm" :model="articleForm" :rules="rules" label-width="80px">
        <el-form-item label="标题" prop="title">
          <el-input v-model="articleForm.title" style="width:700px"></el-input>
        </el-form-item>
        <el-form-item prop="articleBody">
          <quill-editer class="quill_edit" v-model="articleForm.articleBody"></quill-editer>
        </el-form-item>
        <el-form-item label="视频地址" prop="videoURL">
          <el-input v-model="articleForm.videoURL" style="width:700px"></el-input>
        </el-form-item>
        <el-row type="flex" justify="center">
          <el-form-item>
            <el-button type="primary" @click="onSubmit">确定</el-button>
            <el-button>取消</el-button>
          </el-form-item>
        </el-row>
      </el-form>
    </div>
  </div>
</template>

<script>
import { add } from '@/api/hmmm/articles'
import eventBus from '@/utils/eventBus'

export default {
  name: 'ArticleAdd',
  data() {
    return {
      articleForm: {
        title: '',
        articleBody: '',
        videoURL: ''
      },
      rules: {
        title: [
          { required: true, message: '标题不可为空' },
          { min: 5, max: 20, message: '标题长度为5至20个字符' }
        ],
        articleBody: [{ required: true, message: '内容不可为空' }],
        videoURL: [{ required: true, message: '内容不可为空' }]
      }
    }
  },
  methods: {
    onSubmit() {
      this.$refs.articleForm.validate(isOK => {
        if (isOK) {
          add(this.articleForm)
          this.$router.push('/articles/list')
        }
      })
    }
  },
  created() {
      eventBus.$on('editItem', row => {
          this.articleForm = row
      })
  }
}
</script>

<style lang="less" scoped>
.quill_edit {
  height: 400px;
  padding-bottom: 90px;
  min-width: 700px;
  width: 700px;
  background-color: #fff;
}
</style>
