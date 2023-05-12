<template>
  <div>
    <el-breadcrumb separator-class="el-icon-arrow-right" style="font-size: 16px;">
      <el-breadcrumb-item :to="{ path: '/sys-admin' }">
        <i class="el-icon-s-promotion"></i> 后台管理
      </el-breadcrumb-item>
      <el-breadcrumb-item>添加属性</el-breadcrumb-item>
    </el-breadcrumb>

    <el-divider></el-divider>

    <el-form :model="ruleForm" :rules="rules" ref="ruleForm" label-width="130px" class="demo-ruleForm">
      <el-form-item label="属性模板" prop="templateId">
        <el-select v-model="ruleForm.templateId" placeholder="请选择">
          <el-option
              v-for="item in attributeTemplateListOptions"
              :key="item.id"
              :label="item.name"
              :value="item.id">
          </el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="名称" prop="name">
        <el-input v-model="ruleForm.name"></el-input>
      </el-form-item>
      <el-form-item label="简介" prop="description">
        <el-input v-model="ruleForm.description"></el-input>
      </el-form-item>
      <el-form-item label="类型" prop="type">
        <template>
          <el-radio-group v-model="ruleForm.type">
            <el-radio :label="0">非销售属性
              <el-tooltip class="item" effect="dark" content="对商品销售不产生影响，但应补充说明的属性，例如：手机的产地" placement="top">
                <i class="el-icon-info" style="color: #aaa"></i>
              </el-tooltip>
            </el-radio>
            <el-radio :label="1">销售属性<el-tooltip class="item" effect="dark" content="必须确定属性的值才可以进行商品销售的属性，例如：手机的内存容量" placement="top">
              <i class="el-icon-info" style="color: #aaa"></i>
            </el-tooltip></el-radio>
          </el-radio-group>
        </template>
      </el-form-item>
      <el-form-item label="输入类型" prop="inputType">
        <template>
          <el-radio-group v-model="ruleForm.inputType">
            <el-radio :label="0">手动录入</el-radio>
            <el-radio :label="1">单选</el-radio>
            <el-radio :label="2">多选</el-radio>
            <el-radio :label="3">单选（下拉列表）</el-radio>
            <el-radio :label="4">多选（下拉列表）</el-radio>
          </el-radio-group>
        </template>
      </el-form-item>
      <el-form-item label="备选值列表" prop="valueList">
        <el-input v-model="ruleForm.valueList"></el-input>
      </el-form-item>
      <el-form-item label="计量单位" prop="unit">
        <el-input v-model="ruleForm.unit"></el-input>
      </el-form-item>
      <el-form-item label="排序序号" prop="sort">
        <el-input v-model="ruleForm.sort"></el-input>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="submitForm('ruleForm')">添加</el-button>
        <el-button @click="resetForm('ruleForm')">重置</el-button>
      </el-form-item>
    </el-form>
  </div>
</template>

<script>
export default {
  data() {
    return {
      attributeTemplateListOptions: [],
      ruleForm: {
        templateId: '',
        name: '',
        description: '',
        type: 0,
        inputType: 0,
        valueList: '',
        unit: '',
        sort: ''
      },
      rules: {
        templateId: [
          {required: true, message: '请选择属性模板', trigger: 'change'},
        ],
        name: [
          {required: true, message: '请输入属性名称', trigger: 'blur'},
          {min: 2, max: 15, message: '长度在 2 到 15 个字符', trigger: 'blur'}
        ],
        sort: [
          {pattern: /^(\d{1}|[1-9]{1}[0-9]?)$/, message: '自定义排序序号必须是0-99之间的数字', trigger: 'blur'}
        ]
      }
    };
  },
  methods: {
    loadAttributeTemplateList() {
      let url = 'http://localhost:9080/attribute-templates';
      console.log('url = ' + url);
      this.axios
          .create({'headers': {'Authorization': localStorage.getItem('jwt')}})
          .get(url).then((response) => {
        let responseBody = response.data;
        if (responseBody.state == 20000) {
          let attributeTemplateList = responseBody.data;
          this.attributeTemplateListOptions = attributeTemplateList;
        } else {
          this.$message.error(responseBody.message);
        }
      });
    },
    submitForm(formName) {
      this.$refs[formName].validate((valid) => {
        if (valid) {
          let url = 'http://localhost:9080/attributes/add-new';
          console.log('url = ' + url);
          let formData = this.qs.stringify(this.ruleForm, {'arrayFormat': 'repeat'});
          console.log('formData = ' + formData);
          this.axios
              .create({'headers': {'Authorization': localStorage.getItem('jwt')}})
              .post(url, formData).then((response) => {
            console.log('服务器端响应的结果：');
            console.log(response);
            let responseBody = response.data;
            if (responseBody.state == 20000) {
              this.$message({
                message: '添加成功！',
                type: 'success'
              });
              this.resetForm(formName);
            } else {
              this.$message.error(responseBody.message);
            }
          });
        } else {
          console.log('error submit!!');
          return false;
        }
      });
    },
    resetForm(formName) {
      this.$refs[formName].resetFields();
    }
  },
  mounted() {
    this.loadAttributeTemplateList();
  }
}
</script>

<style scoped>
</style>