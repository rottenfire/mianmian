<template>
  <div class="dashboard-container">
    <el-card class="box-card">
      <el-form ref="newQuestionsForm" :rules="formDataRules" :model="formData" label-width="80px">
        <el-form-item label="学科" prop="subjectID">
          <el-select v-model="formData.subjectID" placeholder="请选择">
            <el-option
              v-for="item in subjectList"
              :key="item.value"
              :label="item.label"
              :value="item.value"
            ></el-option>
          </el-select>
        </el-form-item>

        <el-form-item label="目录" prop="catalogID">
          <el-select v-model="formData.catalogID" placeholder="请选择活动区域">
            <el-option
              v-for="item in directoryList"
              :key="item.value"
              :label="item.label"
              :value="item.value"
            ></el-option>
          </el-select>
        </el-form-item>

        <el-form-item label="企业" prop="enterpriseID">
          <el-select v-model="formData.enterpriseID" filterable placeholder="请选择活动区域">
            <el-option
              v-for="item in companyList.items"
              :key="item.id"
              :label="item.company"
              :value="item.id"
            ></el-option>
          </el-select>
        </el-form-item>

        <el-form-item label="城市" prop="city">
          <el-select @change="changeCity" v-model="city" filterable placeholder="请选择">
            <el-option v-for="item in citysList" :key="item" :label="item" :value="item"></el-option>
          </el-select>
          <el-select v-model="formData.city" placeholder="请选择">
            <el-option v-for="item in areaList" :key="item" :label="item" :value="item"></el-option>
          </el-select>
        </el-form-item>

        <el-form-item label="方向" prop="direction">
          <el-select v-model="formData.direction" placeholder="请选择">
            <el-option v-for="item in directionList" :key="item" :label="item" :value="item"></el-option>
          </el-select>
        </el-form-item>

        <el-form-item label="题型" prop="questionType">
          <el-radio-group v-model="formData.questionType" @change="changeType">
            <el-radio
              v-for="item in questionType"
              :key="item.value"
              :label="item.value.toString()"
            >{{item.label}}</el-radio>
          </el-radio-group>
        </el-form-item>

        <el-form-item label="难度" prop="difficulty">
          <el-radio-group v-model="formData.difficulty">
            <el-radio
              v-for="item in difficulty"
              :key="item.value"
              :label="item.value.toString()"
            >{{item.label}}</el-radio>
          </el-radio-group>
        </el-form-item>

        <el-form-item label="题干" prop="question">
          <quill-editor :options="editorOption" class="quill-editor" v-model="formData.question"></quill-editor>
        </el-form-item>

        <el-form-item v-if="formData.questionType !== '3'" class="option" label="选项" prop="options">
          <el-radio-group
            v-model="optionRadio"
            @change="changeOption"
            v-if="formData.questionType === '1'"
          >
            <el-radio
              class="optionItem"
              v-for="(item,index) in formData.options"
              :key="index"
              :label="item.code"
            >
              <el-row type="flex" justify="flex-start" align="middle">
                <span class="optionCode">{{item.code}}</span>
                <el-input class="optionTitle" v-model="item.title" placeholder="请输入内容"></el-input>
                <div class="uploadBox">
                  <i class="el-icon-plus"></i>
                </div>
                <i class="delIcon el-icon-error" @click="delItem(index)"></i>
              </el-row>
            </el-radio>
          </el-radio-group>
          <el-checkbox-group
            v-model="optionList"
            @change="changeOption"
            v-if="formData.questionType === '2'"
          >
            <el-checkbox
              class="optionItem"
              v-for="(item,index) in formData.options"
              :key="index"
              :label="item.code"
            >
              <el-row type="flex" justify="flex-start" align="middle">
                <span class="optionCode">{{item.code}}</span>
                <el-input class="optionTitle" v-model="item.title" placeholder="请输入内容"></el-input>
                <div class="uploadBox">
                  <i class="el-icon-plus"></i>
                </div>
                <i class="delIcon el-icon-error" @click="delItem(index)"></i>
              </el-row>
            </el-checkbox>
          </el-checkbox-group>
        </el-form-item>
        <el-button v-if="formData.questionType !== '3'" class="addItemBtn" @click="addItem">+增加选项及答案</el-button>

        <el-form-item label="解析视频" prop="videoURL">
          <el-input v-model="formData.videoURL" placeholder="请输入视频地址，以http(s)://开头"></el-input>
        </el-form-item>

        <el-form-item label="答案解析" prop="answer">
          <quill-editor :options="editorOption" class="quill-editor" v-model="formData.answer"></quill-editor>
        </el-form-item>

        <el-form-item label="题目备注" prop="remarks">
          <el-input type="textarea" :rows="3" v-model="formData.remarks" placeholder="请输入备注"></el-input>
        </el-form-item>

        <el-form-item label="试题标签" prop="tags">
          <el-input v-model="formData.tags" placeholder="请输入标签"></el-input>
        </el-form-item>
        <el-form-item>
          <el-button type="primary" @click="submitQuestion">提交</el-button>
        </el-form-item>
      </el-form>
    </el-card>
  </div>
</template>

<script>
import { simple as subjectsSimple } from '@/api/hmmm/subjects.js'
import { simple as directorysSimple } from '@/api/hmmm/directorys.js'
import { list as companysList } from '@/api/hmmm/companys.js'
import { provinces, citys as cityArea } from '@/api/hmmm/citys.js'
import { direction, questionType, difficulty } from '@/api/hmmm/constants.js'
import { add } from '@/api/hmmm/questions.js'
export default {
  name: 'QuestionsNew',
  data() {
    const validateOptions = (rules, value, callback) => {
      if (!this.isSubmit) return
      let hasChoice = false
      value.map(item => {
        if (item.isRight) {
          hasChoice = true
        }
        if (!item.title) {
          callback(new Error('所有选择必须有内容'))
        }
      })
      console.log(hasChoice)
      if (hasChoice) {
        callback()
      } else {
        callback(new Error('必须有正确的选项'))
      }
    }
    return {
      formData: {
        subjectID: '',
        catalogID: '',
        enterpriseID: '',
        city: '',
        direction: '',
        questionType: '1',
        difficulty: '',
        question: '',
        options: [
          { code: 'A', title: '', img: '', isRight: false },
          { code: 'B', title: '', img: '', isRight: false },
          { code: 'C', title: '', img: '', isRight: false },
          { code: 'D', title: '', img: '', isRight: false }
        ],
        videoURL: '',
        answer: '',
        remarks: '',
        tags: ''
      },
      formDataRules: {
        subjectID: [
          { required: true, message: '请选择学科', trigger: 'change' }
        ],
        catalogID: [
          { required: true, message: '请选择目录', trigger: 'change' }
        ],
        enterpriseID: [
          { required: true, message: '请选择企业', trigger: 'change' }
        ],
        city: [{ required: true, message: '请选择城市', trigger: 'change' }],
        direction: [
          { required: true, message: '请选择方向', trigger: 'change' }
        ],
        questionType: [
          { required: true, message: '请选择题型', trigger: 'change' }
        ],
        difficulty: [
          { required: true, message: '请选择难度', trigger: 'change' }
        ],
        question: [
          { required: true, message: '请输入题干内容', trigger: 'blur' }
        ],
        options: [
          {
            validator: validateOptions
          }
        ],
        videoURL: [
          { required: true, message: '请输入解析视频', trigger: 'blur' },
          {
            pattern: /^http[s]*/,
            message: '请以http(s)://开头',
            trigger: 'blur'
          }
        ],
        answer: [
          { required: true, message: '请输入答案解析', trigger: 'blur' }
        ],
        remarks: [{ required: true, message: '请输入备注', trigger: 'blur' }],
        tags: [{ required: true, message: '请输入试题标签', trigger: 'blur' }]
      },
      optionRadio: '',
      optionList: [],
      city: '',
      citysList: provinces(),
      areaList: [],
      subjectList: [],
      directoryList: [],
      companyList: [],
      directionList: direction,
      questionType: questionType, // 单选1 多选2 简答3
      difficulty: difficulty,
      disabled: false,
      isSubmit: false,
      editorOption: {
        placeholder: '请在这里输入'
      }
    }
  },
  methods: {
    async getSubjectsList() {
      let result = await subjectsSimple()
      this.subjectList = result.data
    },
    async getDirectorysList() {
      let result = await directorysSimple()
      this.directoryList = result.data
    },
    async getCompanysList() {
      let result = await companysList()
      this.companyList = result.data
    },
    changeCity(value) {
      this.areaList = cityArea(value)
    },
    setImgURL(param) {
      console.log(param)
    },
    addItem() {
      const codeStr = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ'
      let newCode = codeStr.charAt(this.formData.options.length)
      this.formData.options.push({
        code: newCode,
        title: '',
        img: '',
        isRight: false
      })
      this.resetOption()
    },
    delItem(index) {
      this.formData.options.splice(index, 1)
      this.resetOption()
      this.resetCode()
    },
    resetCode() {
      const codeStr = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ'
      this.formData.options = this.formData.options.map((item, index) => {
        item.code = codeStr.charAt(index)
        return item
      })
    },
    changeType(data) {
      this.resetOption()
    },
    changeOption(data) {
      this.formData.options = this.formData.options.map(item => {
        item.isRight = false
        return item
      })
      if (this.formData.questionType === '1') {
        this.optionList = []
        this.formData.options = this.formData.options.map(item => {
          if (item.code === data) {
            item.isRight = true
          }
          return item
        })
      } else {
        this.optionRadio = ''
        const obj = {}
        data.map(item => {
          obj[item] = true
        })
        this.formData.options = this.formData.options.map(item => {
          if (obj[item.code]) {
            item.isRight = true
          }
          return item
        })
      }
    },
    resetOption() {
      this.optionList = []
      this.optionRadio = ''
      this.formData.options = this.formData.options.map(item => {
        item.isRight = false
        return item
      })
    },
    // 将number类型转换为string类型
    formatNumberToString() {
      for (let key in this.formData) {
        if (typeof this.formData[key] === 'number') {
          this.formData[key] = this.formData[key].toString()
        }
      }
      console.log(this.formData)
    },
    // 提交新增问题表单内容
    submitQuestion() {
      for (let i = 0; i < this.formData.options.length; i++) {
        console.log(
          this.formData.options[i].code,
          this.formData.options[i].isRight
        )
      }
      console.log(this.formData)
      this.isSubmit = true
      this.$refs.newQuestionsForm.validate(async isOK => {
        if (isOK) {
          // 通过校验
          let result = await add(this.formData)
          console.log(result)
        }
      })
      this.isSubmit = false
    }
  },
  created() {
    this.getSubjectsList()
    this.getDirectorysList()
    this.getCompanysList()
  }
}
</script>

<style lang="less" scoped>
.option {
  .optionItem {
    display: flex;
    justify-content: flex-start;
    align-items: center;
    margin: 10px 0;
    .optionCode {
      display: inline-block;
      width: 30px;
    }
    .optionTitle {
      margin-right: 20px;
    }
    .uploadBox {
      width: 50px;
      height: 50px;
      display: flex;
      justify-content: center;
      align-items: center;
      border: 1px dashed #ccc;
      border-radius: 5px;
      margin-right: 20px;
      .el-icon-plus {
        width: 50px;
        height: 50px;
        display: flex;
        justify-content: center;
        align-items: center;
        color: #606266;
      }
    }
    .delIcon {
      color: red;
    }
  }
}
.addItemBtn {
  margin: 20px 0 20px 80px;
}
</style>
