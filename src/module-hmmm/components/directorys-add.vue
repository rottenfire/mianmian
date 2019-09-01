<template>
  <div class="dashboard-container">
    <!-- 输入框 -->
    <div class="app-container">
      <span>*目录名称 :</span>
      <el-input
        placeholder="请输入内容"
        v-model="info.directoryName"
        clearable
        maxlength="25"
        class="inputcon"
      ></el-input>
    </div>
    <!-- 按钮 -->
    <div class="button">
      <el-button type="primary" @click="repair.id ? edit(info) :confirm(info)">确定</el-button>
      <el-button @click="cancle">取消</el-button>
    </div>
  </div>
</template>

<script>
// 按需导入目录添加
import { add, update } from '../../api/hmmm/directorys'
export default {
  created() {
    console.log(this.repair)

    this.getinfo()
  },
  name: 'DirectorysAdd',
  data() {
    return {
      // input: '',
      info: {
        id: null,
        directoryName: '',
        subjectID: 1
      }
    }
  },
  methods: {
    async confirm(info) {
      await add(info)
      await this.$emit('confirm', false, '1')
      this.info.directoryName = ''
    },
    async edit(info) {
      // console.log(this.info)
      let res = await update(info)
      console.log(res)
      this.$emit('edit', false)
    },
    cancle() {
      this.$emit('cancle', false)
      this.input = ''
    },
    getinfo() {
      this.info.directoryName = this.repair.directoryName
      this.info.id = this.repair.id
      console.log(this.info)
      
    }
  },
  props: ['repair']
}
</script>

<style lang="less" scoped>
.dashboard-container {
  margin-top: 20px;
  overflow: hidden;
  .app-container {
    span {
      font-weight: 700;
    }
    .inputcon {
      width: 300px;
    }
  }
  .button {
    float: right;
    margin-top: 80px;
  }
}
</style>
