<template>
  <div
    class="app-contain"
    :style="{
      minHeight: '100vh',
      padding: '0',
      margin: '20px auto 60px',
      borderRadius: '0px',
      background: '#fff',
      width: '86%',
      position: 'relative',
      height: '100%',
    }"
  >
    <div class="bread_view">
      <el-breadcrumb separator=">" class="breadcrumb">
        <el-breadcrumb-item class="first_breadcrumb" :to="{ path: '/' }"
          >首页</el-breadcrumb-item
        >
        <el-breadcrumb-item
          class="second_breadcrumb"
          v-for="(item, index) in breadList"
          :key="index"
          >{{ item.name }}</el-breadcrumb-item
        >
      </el-breadcrumb>
    </div>
    <el-form
      ref="formRef"
      :model="form"
      class="add_form"
      label-width="120px"
      :rules="rules"
    >
      <el-row>
        <el-col :span="8">
          <el-form-item label="学号" prop="xuehao">
            <el-select
              class="list_sel"
              :disabled="!isAdd || disabledForm.xuehao ? true : false"
              v-model="form.xuehao"
              placeholder="请选择学号"
              style="width: 100%"
              @change="xuehaoChange"
            >
              <el-option
                v-for="(item, index) in xuehaoLists"
                :label="item"
                :value="item"
              >
              </el-option>
            </el-select>
          </el-form-item>
        </el-col>
        <el-col :span="8">
          <el-form-item label="学生姓名" prop="xueshengxingming">
            <el-input
              class="list_inp"
              v-model="form.xueshengxingming"
              placeholder="学生姓名"
              type="text"
              :readonly="!isAdd || disabledForm.xueshengxingming ? true : false"
            />
          </el-form-item>
        </el-col>

        <el-col :span="8">
          <el-form-item label="班级" prop="banji">
            <el-input
              class="list_inp"
              v-model="form.banji"
              placeholder="班级"
              type="text"
              :readonly="!isAdd || disabledForm.banji ? true : false"
            />
          </el-form-item>
        </el-col>

        <el-col :span="8">
          <el-form-item label="考试名称" prop="kaoshimingcheng">
            <el-input
              class="list_inp"
              v-model="form.kaoshimingcheng"
              placeholder="考试名称"
              type="text"
              :readonly="!isAdd || disabledForm.kaoshimingcheng ? true : false"
            />
          </el-form-item>
        </el-col>

        <el-col :span="8">
          <el-form-item label="分数" prop="fenshu">
            <el-input
              class="list_inp"
              v-model.number="form.fenshu"
              placeholder="分数"
              type="number"
              :readonly="!isAdd || disabledForm.fenshu ? true : false"
            />
          </el-form-item>
        </el-col>

        <el-col :span="8">
          <el-form-item label="排名" prop="paiming">
            <el-input
              class="list_inp"
              v-model.number="form.paiming"
              placeholder="排名"
              type="text"
              :readonly="!isAdd || disabledForm.paiming ? true : false"
            />
          </el-form-item>
        </el-col>

        <el-col :span="8">
          <el-form-item label="登记时间" prop="dengjishijian">
            <el-date-picker
              class="list_date"
              v-model="form.dengjishijian"
              format="YYYY-MM-DD HH:mm:ss"
              value-format="YYYY-MM-DD HH:mm:ss"
              type="datetime"
              style="width: 100%"
              :readonly="!isAdd || disabledForm.dengjishijian ? true : false"
              placeholder="请选择登记时间"
            />
          </el-form-item>
        </el-col>
        <el-col :span="8">
          <el-form-item label="教师工号" prop="jiaoshigonghao">
            <el-input
              class="list_inp"
              v-model="form.jiaoshigonghao"
              placeholder="教师工号"
              type="text"
              :readonly="!isAdd || disabledForm.jiaoshigonghao ? true : false"
            />
          </el-form-item>
        </el-col>

        <el-col :span="8">
          <el-form-item label="教师姓名" prop="jiaoshixingming">
            <el-input
              class="list_inp"
              v-model="form.jiaoshixingming"
              placeholder="教师姓名"
              type="text"
              :readonly="!isAdd || disabledForm.jiaoshixingming ? true : false"
            />
          </el-form-item>
        </el-col>

        <el-col :span="24">
          <el-form-item label="教师意见" prop="jiaoshiyijian">
            <el-input
              v-model="form.jiaoshiyijian"
              placeholder="教师意见"
              type="textarea"
              :readonly="!isAdd || disabledForm.jiaoshiyijian ? true : false"
            />
          </el-form-item>
        </el-col>
      </el-row>
      <div class="formModel_btn_box">
        <el-button class="formModel_cancel" @click="backClick">取消</el-button>
        <el-button class="formModel_confirm" @click="save" type="success">
          保存
        </el-button>
      </div>
    </el-form>
  </div>
</template>
<script setup>
import {
  ref,
  getCurrentInstance,
  watch,
  onUnmounted,
  onMounted,
  nextTick,
  computed,
} from 'vue'
import { useRoute, useRouter } from 'vue-router'
const context = getCurrentInstance()?.appContext.config.globalProperties
const route = useRoute()
const router = useRouter()
//基础信息
const tableName = 'kaoshichengji'
const formName = '考试成绩'
//基础信息
const breadList = ref([
  {
    name: formName,
  },
])
//获取唯一标识
const getUUID = () => {
  return new Date().getTime()
}
//form表单
const form = ref({
  xuehao: '',
  xueshengxingming: '',
  banji: '',
  kaoshimingcheng: '',
  fenshu: '',
  paiming: '',
  jiaoshiyijian: '',
  dengjishijian: '',
  jiaoshigonghao: '',
  jiaoshixingming: '',
})
const formRef = ref(null)
const id = ref(0)
const type = ref('')
const disabledForm = ref({
  xuehao: false,
  xueshengxingming: false,
  banji: false,
  kaoshimingcheng: false,
  fenshu: false,
  paiming: false,
  jiaoshiyijian: false,
  dengjishijian: false,
  jiaoshigonghao: false,
  jiaoshixingming: false,
})
const isAdd = ref(false)
//表单验证
//匹配整数
const validateIntNumber = (rule, value, callback) => {
  if (!value) {
    callback()
  } else if (!context?.$toolUtil.isIntNumer(value)) {
    callback(new Error('请输入整数'))
  } else {
    callback()
  }
}
//匹配数字
const validateNumber = (rule, value, callback) => {
  if (!value) {
    callback()
  } else if (!context?.$toolUtil.isNumber(value)) {
    callback(new Error('请输入数字'))
  } else {
    callback()
  }
}
//匹配手机号码
const validateMobile = (rule, value, callback) => {
  if (!value) {
    callback()
  } else if (!context?.$toolUtil.isMobile(value)) {
    callback(new Error('请输入正确的手机号码'))
  } else {
    callback()
  }
}
//匹配电话号码
const validatePhone = (rule, value, callback) => {
  if (!value) {
    callback()
  } else if (!context?.$toolUtil.isPhone(value)) {
    callback(new Error('请输入正确的电话号码'))
  } else {
    callback()
  }
}
//匹配邮箱
const validateEmail = (rule, value, callback) => {
  if (!value) {
    callback()
  } else if (!context?.$toolUtil.isEmail(value)) {
    callback(new Error('请输入正确的邮箱地址'))
  } else {
    callback()
  }
}
//匹配身份证
const validateIdCard = (rule, value, callback) => {
  if (!value) {
    callback()
  } else if (!context?.$toolUtil.checkIdCard(value)) {
    callback(new Error('请输入正确的身份证号码'))
  } else {
    callback()
  }
}
//匹配网站地址
const validateUrl = (rule, value, callback) => {
  if (!value) {
    callback()
  } else if (!context?.$toolUtil.isURL(value)) {
    callback(new Error('请输入正确的URL地址'))
  } else {
    callback()
  }
}
const rules = ref({
  xuehao: [{ required: true, message: '请输入', trigger: 'blur' }],
  xueshengxingming: [],
  banji: [],
  kaoshimingcheng: [{ required: true, message: '请输入', trigger: 'blur' }],
  fenshu: [
    { required: true, message: '请输入', trigger: 'blur' },
    { validator: validateNumber, trigger: 'blur' },
  ],
  paiming: [
    { required: true, message: '请输入', trigger: 'blur' },
    { validator: validateIntNumber, trigger: 'blur' },
  ],
  jiaoshiyijian: [],
  dengjishijian: [],
  jiaoshigonghao: [],
  jiaoshixingming: [],
})
//学号列表
const xuehaoLists = ref([])
//methods

//methods
//获取info
const getInfo = () => {
  context
    ?.$http({
      url: `${tableName}/info/${id.value}`,
      method: 'get',
    })
    .then((res) => {
      let reg = new RegExp('../../../file', 'g')
      form.value = res.data.data
    })
}
const crossRow = ref('')
const crossTable = ref('')
const crossTips = ref('')
const crossColumnName = ref('')
const crossColumnValue = ref('')
//初始化
const init = (
  formId = null,
  formType = 'add',
  formNames = '',
  row = null,
  table = null,
  statusColumnName = null,
  tips = null,
  statusColumnValue = null
) => {
  form.value.dengjishijian = context?.$toolUtil.getCurDateTime()
  if (formId) {
    id.value = formId
    type.value = formType
  }
  if (formType == 'add') {
    isAdd.value = true
  } else if (formType == 'info') {
    isAdd.value = false
    getInfo()
  } else if (formType == 'edit') {
    isAdd.value = true
    getInfo()
  } else if (formType == 'cross') {
    isAdd.value = true
    // getInfo()
    for (let x in row) {
      if (x == 'xuehao') {
        form.value.xuehao = row[x]
        disabledForm.value.xuehao = true
        continue
      }
      if (x == 'xueshengxingming') {
        form.value.xueshengxingming = row[x]
        disabledForm.value.xueshengxingming = true
        continue
      }
      if (x == 'banji') {
        form.value.banji = row[x]
        disabledForm.value.banji = true
        continue
      }
      if (x == 'kaoshimingcheng') {
        form.value.kaoshimingcheng = row[x]
        disabledForm.value.kaoshimingcheng = true
        continue
      }
      if (x == 'fenshu') {
        form.value.fenshu = row[x]
        disabledForm.value.fenshu = true
        continue
      }
      if (x == 'paiming') {
        form.value.paiming = row[x]
        disabledForm.value.paiming = true
        continue
      }
      if (x == 'jiaoshiyijian') {
        form.value.jiaoshiyijian = row[x]
        disabledForm.value.jiaoshiyijian = true
        continue
      }
      if (x == 'dengjishijian') {
        form.value.dengjishijian = row[x]
        disabledForm.value.dengjishijian = true
        continue
      }
      if (x == 'jiaoshigonghao') {
        form.value.jiaoshigonghao = row[x]
        disabledForm.value.jiaoshigonghao = true
        continue
      }
      if (x == 'jiaoshixingming') {
        form.value.jiaoshixingming = row[x]
        disabledForm.value.jiaoshixingming = true
        continue
      }
    }
    if (row) {
      crossRow.value = row
    }
    if (table) {
      crossTable.value = table
    }
    if (tips) {
      crossTips.value = tips
    }
    if (statusColumnName) {
      crossColumnName.value = statusColumnName
    }
    if (statusColumnValue) {
      crossColumnValue.value = statusColumnValue
    }
  }
  context
    ?.$http({
      url: `${context?.$toolUtil.storageGet('frontSessionTable')}/session`,
      method: 'get',
    })
    .then((res) => {
      var json = res.data.data
      if (
        json.hasOwnProperty('jiaoshigonghao') &&
        context?.$toolUtil.storageGet('frontRole') != '管理员'
      ) {
        form.value.jiaoshigonghao = json.jiaoshigonghao
        disabledForm.value.jiaoshigonghao = true
      }
      if (
        json.hasOwnProperty('jiaoshixingming') &&
        context?.$toolUtil.storageGet('frontRole') != '管理员'
      ) {
        form.value.jiaoshixingming = json.jiaoshixingming
        disabledForm.value.jiaoshixingming = true
      }
    })
  context
    ?.$http({
      url: `option/xuesheng/xuehao`,
      method: 'get',
    })
    .then((res) => {
      xuehaoLists.value = res.data.data
    })
  //由上级字段带出不可改
  disabledForm.value.xueshengxingming = true
  //由上级字段带出不可改
  disabledForm.value.banji = true
}
//初始化
//取消
const backClick = () => {
  history.back()
}
//学号回调
const xuehaoChange = () => {
  context
    ?.$http({
      url: `follow/xuesheng/xuehao?columnValue=` + form.value.xuehao,
      method: 'get',
    })
    .then((res) => {
      //带出学生姓名字段
      if (res.data.data.xueshengxingming) {
        form.value.xueshengxingming = res.data.data.xueshengxingming
      }
      //带出班级字段
      if (res.data.data.banji) {
        form.value.banji = res.data.data.banji
      }
    })
}
//提交
const save = () => {
  var table = crossTable.value
  var objcross = JSON.parse(JSON.stringify(crossRow.value))
  let crossUserId = ''
  let crossRefId = ''
  let crossOptNum = ''
  if (type.value == 'cross') {
    if (crossColumnName.value != '') {
      if (!crossColumnName.value.startsWith('[')) {
        for (let o in objcross) {
          if (o == crossColumnName.value) {
            objcross[o] = crossColumnValue.value
          }
        }
        //修改跨表数据
        changeCrossData(objcross)
      } else {
        crossUserId = context?.$toolUtil.storageGet('userid')
        crossRefId = objcross['id']
        crossOptNum = crossColumnName.value.replace(/\[/, '').replace(/\]/, '')
      }
    }
  }
  formRef.value.validate((valid) => {
    if (valid) {
      if (crossUserId && crossRefId) {
        form.value.crossuserid = crossUserId
        form.value.crossrefid = crossRefId
        let params = {
          page: 1,
          limit: 1000,
          crossuserid: form.value.crossuserid,
          crossrefid: form.value.crossrefid,
        }
        context
          ?.$http({
            url: `${tableName}/page`,
            method: 'get',
            params: params,
          })
          .then((res) => {
            if (res.data.data.total >= crossOptNum) {
              context?.$toolUtil.message(`${crossTips.value}`, 'error')
              return false
            } else {
              context
                ?.$http({
                  url: `${tableName}/${!form.value.id ? 'save' : 'update'}`,
                  method: 'post',
                  data: form.value,
                })
                .then((res) => {
                  context?.$toolUtil.message(`操作成功`, 'success', () => {
                    history.back()
                  })
                })
            }
          })
      } else {
        context
          ?.$http({
            url: `${tableName}/${!form.value.id ? 'save' : 'update'}`,
            method: 'post',
            data: form.value,
          })
          .then((res) => {
            context?.$toolUtil.message(`操作成功`, 'success', () => {
              history.back()
            })
          })
      }
    }
  })
}
//修改跨表数据
const changeCrossData = (row) => {
  context
    ?.$http({
      url: `${crossTable.value}/update`,
      method: 'post',
      data: row,
    })
    .then((res) => {})
}
onMounted(() => {
  type.value = route.query.type ? route.query.type : 'add'
  let row = null
  let table = null
  let statusColumnName = null
  let tips = null
  let statusColumnValue = null
  if (type.value == 'cross') {
    row = context?.$toolUtil.storageGet('crossObj')
      ? JSON.parse(context?.$toolUtil.storageGet('crossObj'))
      : {}
    table = context?.$toolUtil.storageGet('crossTable')
    statusColumnName = context?.$toolUtil.storageGet('crossStatusColumnName')
    tips = context?.$toolUtil.storageGet('crossTips')
    statusColumnValue = context?.$toolUtil.storageGet('crossStatusColumnValue')
  }
  init(
    route.query.id ? route.query.id : null,
    type.value,
    '',
    row,
    table,
    statusColumnName,
    tips,
    statusColumnValue
  )
})
</script>
<style lang="scss" scoped>
// 面包屑盒子
.bread_view {
  border-radius: 0px;
  padding: 12px 20px;
  margin: 0px auto;
  background: none;
  width: 100%;
  border-color: #eee;
  border-width: 0 0 0px;
  position: relative;
  border-style: solid;
  :deep(.breadcrumb) {
    font-size: 14px;
    line-height: 1;
    .el-breadcrumb__separator {
      margin: 0 9px;
      color: #999;
      font-weight: 500;
    }
    .first_breadcrumb {
      .el-breadcrumb__inner {
        color: #333;
        display: inline-block;
      }
    }
    .second_breadcrumb {
      .el-breadcrumb__inner {
        color: #999;
        display: inline-block;
      }
    }
  }
}
// 表单
.add_form {
  border: 1px solid #ddd;
  border-radius: 0px;
  padding: 40px 7% 50px;
  background: url(http://localhost:8080/onlineLearning/file/974a5cd5eb9840c9b1a4576231567b5c.png);
  // form item
  :deep(.el-form-item) {
    border: 0px solid #eee;
    padding: 6px 0;
    margin: 0 0 20px 0;
    background: none;
    display: flex;
    //label
    .el-form-item__label {
      background: none;
      display: block;
      width: auto;
      min-width: 150px;
      text-align: right;
    }
    // 内容盒子
    .el-form-item__content {
      display: flex;
      width: calc(100% - 150px);
      justify-content: flex-start;
      align-items: center;
      flex-wrap: wrap;
      // 输入框
      .list_inp {
        padding: 0 10px;
        background: #fff;
        width: auto;
        border-color: #ccc;
        border-width: 1px;
        line-height: 36px;
        box-sizing: border-box;
        border-style: solid;
        min-width: 100%;
        height: 36px;
        //去掉默认样式
        .el-input__wrapper {
          border: none;
          box-shadow: none;
          background: none;
          border-radius: 0;
          height: 100%;
          padding: 0;
        }
        .is-focus {
          box-shadow: none !important;
        }
      }
      //日期选择器
      .list_date {
        border-radius: 0px;
        background: #fff;
        width: auto;
        border-color: #ccc;
        border-width: 1px;
        line-height: 36px;
        box-sizing: border-box;
        border-style: solid;
        min-width: 100%;
        //去掉默认样式
        .el-input__wrapper {
          border: none;
          box-shadow: none;
          background: none;
          border-radius: 0;
          height: 100%;
        }
      }
      // 下拉框
      .list_sel {
        border-radius: 0px;
        padding: 0 10px;
        background: #fff;
        width: auto;
        border-color: #ccc;
        border-width: 1px;
        line-height: 36px;
        box-sizing: border-box;
        border-style: solid;
        min-width: 100%;
        //去掉默认样式
        .select-trigger {
          height: 100%;
          .el-input {
            height: 100%;
            .el-input__wrapper {
              border: none;
              box-shadow: none;
              background: none;
              border-radius: 0;
              height: 100%;
              padding: 0;
            }
            .is-focus {
              box-shadow: none !important;
            }
          }
        }
      }
      // 长文本
      .el-textarea__inner {
        border: 1px solid #ccc;
        border-radius: 0px;
        padding: 12px;
        color: #666;
        background: #fff;
        width: 100%;
        font-size: 14px;
        min-height: 120px;
      }
    }
  }
}
// 按钮盒子
.formModel_btn_box {
  padding: 0 150px;
  display: flex;
  width: 100%;
  justify-content: center;
  align-items: center;
  .formModel_cancel {
    border: 1px solid #ccc;
    cursor: pointer;
    border-radius: 0px;
    padding: 0 40px;
    margin: 0 20px 0 0;
    color: #999;
    background: #f9f9f9;
    width: auto;
    font-size: 14px;
    line-height: 40px;
    transition: all 0.3s;
    height: 40px;
  }
  .formModel_cancel:hover {
    transform: rotate(0deg) scale(0.9) skew(0deg, 0deg)
      translate3d(0px, -10px, 0px);
  }

  .formModel_confirm {
    border: 0px solid #11396190;
    cursor: pointer;
    border-radius: 0px;
    padding: 0 40px;
    margin: 0 20px 0 0;
    color: #fff;
    background: #3a73ab;
    width: auto;
    font-size: 14px;
    line-height: 40px;
    transition: all 0.3s;
    height: 40px;
  }
  .formModel_confirm:hover {
    transform: rotate(0deg) scale(0.9) skew(0deg, 0deg)
      translate3d(0px, -10px, 0px);
  }
}
</style>
