<template>
  <el-card class="box-card">
 <el-table
   :data="Data"
    border
    style="width: 100%">
    <el-table-column
     width="70"
      type="index"
      label="序号"
      prop="id"
       > </el-table-column>
       <el-table-column
       prop="addTime"
      label="组题时间"
      width="180">
    </el-table-column>
    <el-table-column
      prop="userName"
      label="用户名"
      width="100">
    </el-table-column>
    <el-table-column
      prop="questionIDs[0]"
      label="试题ID">
    </el-table-column>
     <el-table-column
      prop="progressOfAnswer"
      label="答题进度">
    </el-table-column>
   <el-table-column
      prop="accuracyRate"
      label="正确率">
    </el-table-column>
    <el-table-column
      prop="totalSeconds"
      label="答题总耗时(秒)">
    </el-table-column>
    <el-table-column
      prop="questionType"
      label="组题类型/详情">
    </el-table-column>
    <el-table-column
    width="90"
      prop="name"
      label="操作">
      <template slot-scope="obj">
       <el-button @click="delcontent(obj.row)">删除</el-button>
       </template>
    </el-table-column>

  </el-table>
</el-card>
</template>

<script>
import {randoms, removeRandoms} from '@/api/hmmm/questions'

export default {
  name: 'QuestionsRandoms',
  data() {
    return {
    Data: []
  }
},
methods: {
   async delcontent(row) {
      let confirm = await this.$confirm('请问您是否要删除', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).catch(() => {
        this.$message.info('已经取消')
      })
      if (confirm) {
        console.log(row)
          let list = await removeRandoms(row.toString())
          console.log(list)
      }

      // }

   },
   async getRandoms () {
     let newData = await randoms()
    console.log(newData.data.items)
    this.Data = newData.data.items
   }

},
created() {
    this.getRandoms()
}

}
</script>

<style  scoped>

</style>
