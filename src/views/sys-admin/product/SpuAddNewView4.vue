<template>
  <div>
    <el-breadcrumb separator-class="el-icon-arrow-right" style="font-size: 16px;">
      <el-breadcrumb-item :to="{ path: '/' }">
        <i class="el-icon-s-promotion"></i> 后台管理
      </el-breadcrumb-item>
      <el-breadcrumb-item>商品管理</el-breadcrumb-item>
      <el-breadcrumb-item>新增SPU</el-breadcrumb-item>
      <el-breadcrumb-item>第4步：填写商品详情</el-breadcrumb-item>
    </el-breadcrumb>

    <el-divider></el-divider>

    <el-form :model="ruleForm" ref="ruleForm" label-width="130px" class="demo-ruleForm">
      <el-form-item label="基本信息">
        <el-descriptions :title="ruleForm.name">
          <el-descriptions-item label="品牌">
            <el-tag size="small">{{ruleForm.brandName}}</el-tag>
          </el-descriptions-item>
          <el-descriptions-item label="类别" :span="2">{{ruleForm.categoryName}}</el-descriptions-item>
          <el-descriptions-item label="标题" :span="3">{{ruleForm.title}}</el-descriptions-item>
          <el-descriptions-item label="简介" :span="3">{{ruleForm.description}}</el-descriptions-item>
          <el-descriptions-item label="相册" :span="3">{{ruleForm.albumName}}</el-descriptions-item>
          <el-descriptions-item label="货号">{{ruleForm.typeNumber}}</el-descriptions-item>
          <el-descriptions-item label="售价">{{ruleForm.listPrice}}</el-descriptions-item>
          <el-descriptions-item label="计件单位">{{ruleForm.unit}}</el-descriptions-item>
          <el-descriptions-item label="库存">{{ruleForm.stock}}</el-descriptions-item>
          <el-descriptions-item label="库存阈警值">{{ruleForm.stockThreshold}}</el-descriptions-item>
          <el-descriptions-item label="排序序号">{{ruleForm.sort}}</el-descriptions-item>
          <el-descriptions-item label="关键词">{{ruleForm.keywords}}</el-descriptions-item>
          <el-descriptions-item label="标签" :span="2">{{ruleForm.tags}}</el-descriptions-item>
        </el-descriptions>
      </el-form-item>
      <el-form-item label="商品详情">
        <div id="wangEditor"></div>
      </el-form-item>
      <el-form-item>
        <el-button type="warning" @click="backToPreStep()">上一步</el-button>
        <el-button type="primary" @click="submitForm('ruleForm')">提交</el-button>
        <el-button @click="resetForm('ruleForm')">重置</el-button>
      </el-form-item>
    </el-form>
  </div>
</template>

<script>
export default {
  data() {
    return {
      ruleForm: {
        detail: ''
      },
      editor: {}
    };
  },
  methods: {
    initWangEditor() {
      this.editor = new this.wangEditor('#wangEditor');
      this.editor.config.zIndex = 1;
      this.editor.create();
    },
    backToPreStep() {
      this.$router.push('add-new-3');
    },
    loadLocalRuleForm() {
      let localRuleFormString = localStorage.getItem('localRuleForm');
      let localRuleForm = JSON.parse(localRuleFormString);

      if (!localRuleForm || !localRuleForm.categoryId) {
        this.$router.push('add-new-1');
        return;
      }

      if (!localRuleForm.brandId) {
        this.$router.push('add-new-2');
        return;
      }

      if (!localRuleForm.albumId) {
        this.$router.push('add-new-3');
        return;
      }

      this.ruleForm = localRuleForm;
      console.log(this.ruleForm);
    },
    submitForm(formName) {
      this.$refs[formName].validate((valid) => {
        if (valid) {
          let url = 'http://localhost:9080/spu/add-new';
          console.log('url = ' + url);

          this.ruleForm.detail = this.editor.txt.html();
          let formData = this.qs.stringify(this.ruleForm);
          console.log('formData = ' + formData);

          this.axios
              .create({'headers': {'Authorization': localStorage.getItem('jwt')}})
              .post(url, formData).then((response) => {
            let responseBody = response.data;
            if (responseBody.state == 20000) {
              let message = '新增SPU成功！';
              this.$alert(message, '提示', {
                confirmButtonText: '确定',
                callback: action => {
                  this.$router.push('/');
                  localStorage.removeItem('localRuleForm');
                }
              });
            } else {
              let message = responseBody.message;
              this.$alert(message, '警告', {
                confirmButtonText: '确定',
                callback: action => {
                }
              });
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
    this.loadLocalRuleForm();
    this.initWangEditor();
  }
}
</script>