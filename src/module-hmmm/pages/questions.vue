<template>
  <div class="dashboard-container">
    <div class="app-container">
      <el-row class="button">
        <el-button type="primary">新增试题</el-button>
        <el-button type="primary">批量导入</el-button>
      </el-row>
      <el-form ref="form" :model="formData">
        <!-- 第一行 -->
        <el-row class="select" type="flex" justify="space-around">
          <!-- 学科 -->
          <el-form-item label="学科:" prop="subjectList_data" label-width="60px">
            <el-select class="el-select" v-model="formData.subjectList_data" placeholder="请选择">
              <el-option
                v-for="item in formData.subjectList"
                :key="item.value"
                :label="item.label"
                :value="item.value"
              ></el-option>
            </el-select>
          </el-form-item>
          <!-- 难度 -->
          <el-form-item label="难度" prop="itemDiffcult_label" label-width="50px">
            <el-select v-model="formData.itemDiffcult_label" placeholder="请选择">
              <el-option
                v-for="item in formData.itemDiffcult"
                :key="item.value"
                :label="item.label"
                :value="item.value"
              ></el-option>
            </el-select>
          </el-form-item>
          <!-- 试题类型 -->
          <el-form-item label="试题类型:" prop="questionType_label" label-width="90px">
            <el-select v-model="formData.questionType_label" placeholder="请选择">
              <el-option
                v-for="item in formData.questionType"
                :key="item.value"
                :label="item.label"
                :value="item.value"
              ></el-option>
            </el-select>
          </el-form-item>
          <!-- 标签 -->
          <el-form-item label="标签:" prop="tagsList_data" label-width="60px">
            <el-select v-model="formData.tagsList_data" placeholder="请选择">
              <el-option
                v-for="item in formData.tagsList"
                :key="item.value"
                :label="item.label"
                :value="item.value"
              ></el-option>
            </el-select>
          </el-form-item>
          <!-- 城市 -->
          <el-form-item label="城市:" prop="searchForm_province" label-width="60px">
            <el-select
              @change="selectCity(formData.searchForm_province)"
              v-model="formData.searchForm_province"
              placeholder="请选择"
            >
              <el-option v-for="item in provinceList" :key="item" :label="item" :value="item"></el-option>
            </el-select>
          </el-form-item>
          <el-form-item prop="searchForm_city" label-width="20px">
            <el-select
              v-model="formData.searchForm_city"
              collapse-tags
              style="margin-left: 20px;"
              placeholder="请选择"
            >
              <el-option v-for="item in cityList" :key="item" :label="item" :value="item"></el-option>
            </el-select>
          </el-form-item>
        </el-row>
        <!-- 第二行 -->
        <el-row class="input" type="flex" justify="space-around">
          <el-form-item label="关键字:" prop="keyword" label-width="60px">
            <el-input v-model="formData.keyword" placeholder="请输入题目编号/题干" style="width:165px"></el-input>
          </el-form-item>
          <el-form-item label="题目备注:" prop="remarks" label-width="80px">
            <el-input v-model="formData.remarks" placeholder="请输入" style="width:160px"></el-input>
          </el-form-item>
          <el-form-item label="企业简称:" prop="enterpriseID" label-width="80px">
            <el-input v-model="formData.enterpriseID" placeholder="请输入" style="width:165px"></el-input>
          </el-form-item>
          <el-form-item label="方向:" prop="directionItem" label-width="50px">
            <el-select
              class="el-select"
              v-model="formData.directionItem"
              placeholder="请选择"
              style="width:160px"
            >
              <el-option
                v-for="directionItem in formData.directionList"
                :key="directionItem"
                :label="directionItem"
                :value="directionItem"
              ></el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="录入人:" prop="lururen" label-width="60px">
            <el-select
              class="el-select"
              v-model="formData.lururen"
              placeholder="请选择"
              style="width:160px"
            >
              <el-option
                v-for="item in formData.users"
                :key="item.id"
                :label="item.username"
                :value="item.id"
              ></el-option>
            </el-select>
          </el-form-item>
        </el-row>
        <!-- 第三行 -->
        <el-row class="subject" type="flex" justify="space-between">
          <el-form-item label-width="80px" prop="catalogID" label="二级目录:">
            <el-input v-model="formData.catalogID" placeholder="请输入二级目录" style="width:160px;"></el-input>
          </el-form-item>
          <el-form-item label-width="80px">
            <el-button @click="getClear">清除</el-button>
            <el-button type="primary">搜索</el-button>
          </el-form-item>
        </el-row>
      </el-form>
      <!-- 表格 -->
      <el-table :data=list border style="width: 100%">
        <el-table-column prop="id" label="序号" width="80"></el-table-column>
        <el-table-column prop="number" label="试题编号" width="100"></el-table-column>
        <el-table-column  prop="subjectID" label="学科" width="80"></el-table-column>
        <el-table-column :formatter="fmQuestionType" prop="questionType" label="题型" width="80"></el-table-column>
        <el-table-column prop="question" label="题干" width="160"></el-table-column>
        <el-table-column  prop="addDate" label="录入时间" width="220">
          <span slot-scope='stData'>{{stData.row.addDate | parseTimeByString('{y}-{m}-{d} {h}:{i}:{s}')}}</span>
        </el-table-column>
        <el-table-column :formatter="fmDifficulty" prop="difficulty" label="难度" width="80"></el-table-column>
        <el-table-column prop="address" label="使用次数" width="80"></el-table-column>
        <el-table-column prop="creator" label="录入人" width="120"></el-table-column>
        <el-table-column prop="address" label="操作">
          <!-- 使用作用域插槽 -->
          <template slot-scope='stData'>
            <el-button type="primary">预览</el-button>
            <el-button type="primary">修改</el-button>
            <el-button type="primary" @click='delItem(stData.row)'>删除</el-button>
            <el-button type="primary">加入精选</el-button>
          </template>
        </el-table-column>
      </el-table>
      <!-- 分页 -->
      <el-row type="flex" justify="center" style="margin-top:10px">
        <el-col :span="12">
          <el-row type="flex" justify="end">
            <el-col :span="12">
              <el-pagination
                class="pagination"
                background
                layout="prev, pager, next"
                :total="pageList.pages"
                :current-page="pageList.page"
                :page-size="pageList.pagesize"
                @current-change="changePage"
              ></el-pagination>
            </el-col>
          </el-row>
        </el-col>

        <el-col :span="12">
          <el-select
            class="el-select"
            v-model="pageList.pages"
            placeholder="请选择"
            style="width:90px"
          >
            <el-option
              v-for="item in pageList"
              :key="item.value"
              :label="item.label"
              :value="item.value"
            ></el-option>
          </el-select>跳至
          <el-input style="width:50px"></el-input>页
        </el-col>
      </el-row>
    </div>
  </div>
</template>

<script>
// 按需加载
import { remove, list as getTableList } from '@/api/hmmm/questions' // 删除. 基础题库列表
import { simple } from '@/api/hmmm/subjects' // 学科类型
import { difficulty, questionType, direction } from '@/api/hmmm/constants' // 试题难度 类型 ,方向
import { provinces, citys } from '@/api/hmmm/citys' // 城市
import { simple as tagsSimple } from '@/api/hmmm/tags' // 标签简单列表
import { list as getusers } from '@/api/base/users' // 录入人

export default {
  name: 'QuestionsList',
  data() {
    return {
      formData: {
        // 定义学科
        subjectList: [],
        subjectList_data: '',
        // 难度
        itemDiffcult_label: '',
        itemDiffcult: [],
        // 试题类型
        questionType_label: '',
        questionType: [],
        // 标签
        tagsList_data: '',
        tagsList: [],
        // 城市
        searchForm_province: '',

        // 地区
        searchForm_city: '',
        // 地区信息

        // 城市信息---解决无法选中信息
        searchForm: {
          province: '',
          citys: '',
          subjectID: 0
        },
        // 关键字
        keyword: '',
        // 题目备注
        remarks: '',
        // 企业简介
        enterpriseID: '',
        // 方向
        directionList: [],
        directionItem: '',
        // 录入人
        users: [],
        lururen: '',
        // 二级目录
        catalogID: ''
      },
      provinceList: [],
      cityList: citys,
      // 基础题库列表
      list: [],
      // 分页信息
      pageList: {
        page: 1, // 当前页码
        pages: 0, // 总页数
        pagesize: 5 // 每页数量
      }
    }
  },
  // 生命周期函数
  async created() {
    // 获取学科信息
    var subjectInfo = await simple()
    // 录入人
    let {
      data: { list }
    } = await getusers()

    // formData表单
    this.formData = {
      // 把学科信息赋值给subjectList
      subjectList: subjectInfo.data,

      // 获取难度
      itemDiffcult: difficulty,

      // 获取试题类型
      questionType: questionType,

      // 录入人
      users: list // list 录入人导出
    }

    this.provinceList = provinces()
    // 获取标签信息
    var tagsInfo = await tagsSimple()

    // 把标签信息赋值给tagsList
    this.formData.tagsList = tagsInfo.data

    // 方向
    this.formData.directionList = direction

    // 获取基础题库列表数据
    this.getDataList()
    
  },
  methods: {
    async getDataList () {
      let expList = await getTableList()
      this.list = expList.data.items // 将后台数据data.items 赋值给list
    },
    // 删除列表项
    async delItem(info) {
      // 确认框
     
      let {data: {success}} = await remove(info)
      // 更新数据
      success && this.getDataList()
    },
    async getClearQuestion() {
      let item = await list()
      this.page.total = item.data.counts
      this.quslist = item.data.items
    },
    // 时间格式化
    parseTimeByString() {
      
    },
    // 题型类型转换
    fmQuestionType(row, column, newvalue, index) {
          if (newvalue === '1') {
            return '单选'
          } else if (newvalue === '2') {
            return '多选'
          } else {
            return '简答'
          }
    },
    // 题型难度转换
    fmDifficulty(row, column, newvalue, index) {
      if (newvalue === '1') {
        return '简单'
      } else if (newvalue === '2') {
        return '一般'
      } else {
        return '困难'
      }
    },
    // 清空搜索框功能
    getClear() {
      // this.$refs.form.resetFields()
      this.formData = {}
    },
    // 页码改变时触发
    changePage(newPage) {
      // 用最新页码去查询
      this.pageList.page = newPage // 将单签最新页码赋值给data中的变量
      this.getTableList() // 获取当前newPage的数据
    },
    // 城市关联地区方法
    selectCity(pm) {
      console.log(123)
      this.cityList = citys(pm) // pm 将省份与城市对应起来
    }
  }
}
</script>

<style lang='less' scoped>
.app-container {
  .button {
    margin-bottom: 20px;
  }
  .subject {
    margin-top: 10px;
    margin-bottom: 10px;
  }
}
</style>
