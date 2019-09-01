<template>
<el-card>
 <div class="nav">
   <el-row>
<el-button size="small">中等按钮</el-button>
<el-button size="small">中等按钮</el-button>
</el-row>
 </div>

<el-form  class="demo-form-inline" :inline="true"
>
  <el-row>

  <el-form-item label="学科" >
    <el-select  placeholder="请选择" style="width:100px" v-model="searchFrom.subjectID">
      <el-option v-for="item in subjectsList"
       :key="item.value"
       :value="item.value"
       :label="item.label"
      ></el-option>

    </el-select>
  </el-form-item>
  <el-form-item label="难度">
    <el-select  placeholder="请选择"
     style="width:100px"
     v-model="searchFrom.difficulty">
      <el-option
      v-for="item in difficultyList"
      :key= "item.value"
      :label="item.label"
      :value="item.label"
      ></el-option>
    </el-select>
  </el-form-item>
  <el-form-item label="试题类型">
    <el-select  placeholder="请选择" style="width:100px" v-model="searchFrom.type">
      <el-option
      v-for="item in questionTypeList"
      :key="item.value"
      :label="item.label"
       :value="item.label"
      ></el-option>

    </el-select>
  </el-form-item>
  <el-form-item label="标签">
    <el-select  placeholder="请选择" style="width:100px">
      <el-option label="区域" value="shanghai"></el-option>
      <el-option label="区域二" value="beijing"></el-option>
    </el-select>
  </el-form-item>
  <el-form-item label="城市">
    <el-select  placeholder="请选择" style="width:100px" v-model="searchFrom.provinces">
      <el-option
      v-for="item in provincesList"
      :key="item"
      :label="item"
      :value="item"
      ></el-option>
    </el-select >
     <el-select  placeholder="请选择" style="width:100px" v-model="searchFrom.citys">
      <el-option label="区域" value="shanghai"
      v-for="item in cityList"
      :key="item"
      :label="item"
      :value="item"
      ></el-option>
    </el-select>
  </el-form-item>
  <el-form-item label="关键字">
    <el-select  placeholder="请输入编号" style="width:100px">
      <el-option label="区域" value="shanghai"></el-option>
      <el-option label="区域二" value="beijing"></el-option>
    </el-select>
  </el-form-item>
  <el-form-item label="题目备注">
    <el-select  placeholder="请选择" style="width:100px">
      <el-option label="区域" value="shanghai"></el-option>
      <el-option label="区域二" value="beijing"></el-option>
    </el-select>
  </el-form-item>
  <el-form-item label="企业简称">
    <el-select  placeholder="请选择" style="width:100px">
      <el-option label="区域" value="shanghai"></el-option>
      <el-option label="区域二" value="beijing"></el-option>
    </el-select>
  </el-form-item>
  <el-form-item label="方向">
    <el-select  placeholder="请选择" style="width:100px">
      <el-option label="区域" value="shanghai"></el-option>
      <el-option label="区域二" value="beijing"></el-option>
    </el-select>
  </el-form-item>
  <el-form-item label="入录人">
    <el-select  placeholder="请选择" style="width:100px">
      <el-option label="区域" value="shanghai"></el-option>
      <el-option label="区域二" value="beijing"></el-option>
    </el-select>
  </el-form-item>
  <el-form-item label="二级目录">
    <el-select  placeholder="请输入二级目录" style="width:100px">
      <el-option label="区域" value="shanghai"></el-option>
      <el-option label="区域二" value="beijing"></el-option>
    </el-select>
  </el-form-item>
  </el-row>
  <el-form-item>
    <el-button type="primary">查询</el-button>
    <el-button type="primary">清除</el-button>
  </el-form-item>
</el-form>
 <el-table
   :data="list"
    style="width:100%">
    <el-table-column

      label="序号"
      prop="id"
      type="index"
      width="50">
    </el-table-column>
    <el-table-column

      prop="number"
      property="date"
      label="试题编号"
      width="180">
    </el-table-column>
    <el-table-column
      prop="subjectID"
      property="name"
      label="学科"
      width="100">
    </el-table-column>
    <el-table-column
     prop="questionType"
      width="120"
      property="address"
      label="题型">
    </el-table-column>
     <el-table-column
      prop="question"
      property="name"
      label="题干"
      width="120">
    </el-table-column>
     <el-table-column
     prop="addDate"
      property="name"
      label="录入时间"
      width="120">
    </el-table-column>
     <el-table-column
     prop="difficulty"
      property="name"
      label="难度"
      width="120">
    </el-table-column>
     <el-table-column
      property="name"
      label="使用次数"
      width="120">
    </el-table-column>
     <el-table-column
     prop="creator"
      property="name"
      label="录入人"
      width="100">
    </el-table-column>
     <el-table-column
      property="name"
      label="操作"
      width="270">
      <el-row>
      <el-button type="text">预览</el-button>
      <el-button type="text">修改</el-button>
      <el-button type="text">删除</el-button>
      <el-button type="text">加入精选</el-button>
      </el-row>
    </el-table-column>
  </el-table>

</el-card>
</template>

<script>
import {list} from '@/api/hmmm/questions'
import {simple} from '@/api/hmmm/subjects'
import {difficulty, questionType} from '@/api/hmmm/constants'
import {provinces, citys} from '@/api/hmmm/citys'
export default {
  name: 'QuestionsList',
  data() {
    return {
      list: [],
      subjectsList: [],
      difficultyList: [],
      questionTypeList: [], // 设置数组方便遍历
      provincesList: [], // 城市数组
      cityList: [], // 城市
// 提交搜索表单集合
      searchFrom: {
      subjectID: '',
      difficulty: '',
      type: '',
      provinces: '',
      citys: ''
      }
    }
  },
methods: {
    //  列表数据
    async getlist() {
          let newData = await list()
          this.list = newData.data.items

   },
  //  学科列表
   async getSubjects () {
           let simpleData = await simple()
          this.subjectsList = simpleData.data
   },
    getDifficulty () {

      this.difficultyList = difficulty

     },
    //  类型
     getquestionType () {
       this.questionTypeList = questionType
      //  console.log(this.questionTypeList)
     },
    //  省会
    getprovinces() {
       this.provincesList = provinces()
    },
    // 城市
    getcitys (pm) {
      this.cityList = citys(pm)
      console.log(this.cityList)
    }

},

 created() {
          //  let newData = await list()
          //  this.list = newData.data.items
          //  console.log(newData.data.items)
        this.getlist()
        this.getSubjects()
        // 程度
        this.getDifficulty()
        // ti题型
        this.getquestionType()
        // 省会
        this.getprovinces()
        // 城市
        this.getcitys(pm)
        // this.difficultyList = difficulty
        // console.log(this.difficultyList)
}}
</script>

<style scoped>
.nav{
  margin-bottom:10px;
  color:red;
}
</style>
