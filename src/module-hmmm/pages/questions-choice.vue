<template>
  <div class="dashboard-container">
    <div class="title">
      <el-button type="primary">新增试题</el-button>
      <el-button type="primary">批量导入</el-button>
    </div>
    <el-form :inline="true">
      <el-form-item label="学科">
        <el-select v-model="formData.subjectID"  placeholder="请选择">
          <el-option v-for="item in subjectsList" :key="item.value" :label="item.label" :value="item.value"></el-option>
        </el-select>
      </el-form-item>
      <el-form-item  label="状态">
        <el-select v-model="formData.chkState" placeholder="请选择">
          <el-option v-for="item in chkTypeList" :key="item.value" :label="item.label" :value="item.value"></el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="难度">
        <el-select v-model="formData.difficulty" placeholder="请选择">
          <el-option v-for="item in difficultyList" :key="item.value" :label="item.label" :value="item.value"></el-option>
        </el-select>
      </el-form-item>
      <el-form-item  label="试题类型">
        <el-select v-model="formData.questionType" placeholder="请选择">
          <el-option v-for="item in questionTypeList" :key="item.value" :label="item.label" :value="item.value"></el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="标签">
        <el-input v-model="formData.tags" placeholder="请输入"></el-input>
      </el-form-item>
      <el-form-item label="录入人">
        <el-input v-model="formData.creatorID" placeholder="请输入"></el-input>
      </el-form-item>
      <el-form-item label="关键字">
        <el-input v-model="formData.keyword" @change="getList" placeholder="请输入"></el-input>
      </el-form-item>
      <el-form-item label="题目备注">
        <el-input v-model="formData.remarks" placeholder="请输入"></el-input>
      </el-form-item>
      <el-form-item label="二级目录">
        <el-input v-model="formData.catalogID" placeholder="请输入"></el-input>
      </el-form-item>
      <el-form-item label="城市">
        <el-select v-model="formData.province" @change="getcity(formData.province)" placeholder="请选择">
          <el-option v-for="item in provincesList" :key="item" :label="item" :value="item"></el-option>
        </el-select>
        <el-select v-model="formData.city" placeholder="请选择">
          <el-option v-for="item in citysList" :key="item" :label="item" :value="item"></el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="企业简称">
        <el-input placeholder="请输入"></el-input>
      </el-form-item>
      <el-form-item label="方向">
        <el-input placeholder="请输入"></el-input>
      </el-form-item>
      <el-button @click="clearFormData">清除</el-button>
      <el-button type="primary" @click="getList">搜索</el-button>
    </el-form>
    <el-tabs type="border-card">
      <el-tab-pane label="全部">
        <el-table :data="choice" border style="width: 100%">
          <el-table-column width="50px" prop="id" label="序号"></el-table-column>
          <el-table-column width="200px" prop="number" label="试题编号"></el-table-column>
          <el-table-column prop="subjectID" :formatter="subjectIDfun"  label="学科"></el-table-column>
          <el-table-column prop="questionType" :formatter='formatterType' label="题型"></el-table-column>
          <el-table-column width="150px" prop="question" label="题干"></el-table-column>
          <el-table-column width="200px" prop="addDate" label="录入时间"></el-table-column>
          <el-table-column width="150px" prop="creator" label="录入人"></el-table-column>
          <el-table-column prop="difficulty" :formatter='formatterDifficulty' label="难度"></el-table-column>
          <el-table-column prop label="使用次数"></el-table-column>
          <el-table-column prop="chkState" :formatter="formatterchkState" label="审核状态"></el-table-column>
          <el-table-column width="150px" prop="chkRemarks" label="审核意见"></el-table-column>
          <el-table-column width="100px" prop="chkUser" label="审核人"></el-table-column>
          <el-table-column prop="publishState" :formatter="formatterPublishState" label="发布状态"></el-table-column>
          <el-table-column width="230px" label="操作">
            <template slot-scope="stData">
              <el-button type="text">审核</el-button>
              <el-button type="text">预览</el-button>
              <el-button type="text">下架</el-button>
              <el-link href="">修改</el-link>
              <el-button type="text" @click="del(stData.row)">删除</el-button>
            </template>
          </el-table-column>
        </el-table>
      </el-tab-pane>
      <el-tab-pane label="待审核">待审核</el-tab-pane>
      <el-tab-pane label="已审核">已审核</el-tab-pane>
      <el-row class="pagination">
      <el-pagination @current-change="changePage" :current-page="formData.currentPage" :page-size="formData.pageSize" background layout="prev, pager, next" :total="formData.total"></el-pagination>
    </el-row>
    </el-tabs>
  </div>
</template>

<script>
import {simple} from '@/api/hmmm/subjects'
import {provinces, citys} from '@/api/hmmm/citys'
import {difficulty, chkType, questionType} from '@/api/hmmm/constants'
import {choice, remove} from '@/api/hmmm/questions'
export default {
  name: 'QuestionsChoice',
  data() {
    return {
      provincesList: [],
      citysList: [],
      subjectsList: [],
      difficultyList: [], // 难度
      chkTypeList: [], // 审核状态
      questionTypeList: [], // 试题类型
      choiceList: {},
      choice: [],
      formData: {
        subjectID: '',
        chkState: '',
        province: '',
        city: '',
        difficulty: '',
        questionType: '',
        tags: '',
        creatorID: '',
        keyword: '',
        remarks: '',
        catalogID: '',
        pageSize: 10,
        currentPage: 1,
        total: 1000
      },
      page: {
        
      }
    }
  },
  methods: {
    // 删除指定题目
    del(id) {
      this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
           remove(id)
           this.getChoiceList()
        })
    },
    // 根据条件获取数据
    async getList() {
      let params = this.formData
      this.getChoiceList({...params}) 
    },
    // 加载表格数据
    async getChoiceList(params) {
      // 获取难度选项
      this.difficultyList = difficulty
      // 获取审核状态选项
     this.chkTypeList = chkType
     // 获取题目类型选项
     this.questionTypeList = questionType
      this.choiceList = await choice(params)
    //  console.log(this.choiceList)
     this.choice = (this.choiceList.data.items)
    //  console.log(this.choice)     
    this.formData.total = this.choiceList.data.pages
    },
    // 清除选项
    clearFormData() {
      this.formData = {}
    },
    // 分页当前页改变
    changePage(newPage) {
      this.page.currentPage = newPage
    },
    getcity(pm) {
      this.citysList = citys(pm) 
      // 获取城市地区      
    },
    formatterType(row, column, cellValue, index) {
      // console.log(cellValue)
      if (cellValue === '1') {
        return '单选'
      } else if (cellValue === '2') {
        return '多选'
      } else {
        return '简答'
      }
    },
    subjectIDfun(row, column, cellValue, index) {
      if (cellValue === 1) {
        return 'java'
      } else if (cellValue === 2) {
        return 'ios'
      } else if (cellValue === 3) {
        return '安卓'
      } else if (cellValue === 4) {
        return '前端'
      } else if (cellValue === 5) {
        return '设计'
      } else if (cellValue === 6) {
        return '产品'
      } else if (cellValue === 7) {
        return 'c++'
      } else if (cellValue === 8) {
        return '数据库'
      } else if (cellValue === 9) {
        return '算法'
      } else if (cellValue === 10) {
        return '运维'
      } else if (cellValue === 11) {
        return 'php'
      } else if (cellValue === 12) {
        return 'C#'
      } else if (cellValue === 13) {
        return 'C'
      } else if (cellValue === 14) {
        return 'python'
      } else if (cellValue === 15) {
        return '大数据'
      } 
    },
    formatterDifficulty(row, column, cellValue, index) {
      if (cellValue === '1') {
        return '简单'
      } else if (cellValue === '2') {
        return '一般'
      } else {
        return '困难'
      }
    },
    formatterchkState(row, column, cellValue, index) {
      if (cellValue === 0) {
        return '待审核'
      } else if (cellValue === 1) {
        return '通过'
      } else {
        return '拒绝'
      }
    },
    formatterPublishState(row, column, cellValue, index) {
      if (cellValue === 0) {
        return '待发布'
      } else if (cellValue === 1) {
        return '已发布'
      } else {
        return '已下架'
      }
    }
  },
  async created() {
     let subjects = await simple()
     this.subjectsList = subjects.data
     this.provincesList = await provinces()
    this.getChoiceList()
  }
}
</script>

<style  scoped>
.dashboard-container {
  padding: 10px;
}
.title {
  margin-bottom: 10px;
}
.pagination {
  margin-top: 20px;
  display: flex;
  justify-content: center;
}
</style>
