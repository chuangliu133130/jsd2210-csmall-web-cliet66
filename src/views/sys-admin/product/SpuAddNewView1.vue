<template>
  <div>
    <el-breadcrumb separator-class="el-icon-arrow-right" style="font-size: 16px;">
      <el-breadcrumb-item :to="{ path: '/' }">
        <i class="el-icon-s-promotion"></i> 后台管理
      </el-breadcrumb-item>
      <el-breadcrumb-item>商品管理</el-breadcrumb-item>
      <el-breadcrumb-item>新增SPU</el-breadcrumb-item>
      <el-breadcrumb-item>第1步：选择商品类别</el-breadcrumb-item>
    </el-breadcrumb>

    <el-divider></el-divider>

    <el-form :model="ruleForm" :rules="rules" ref="ruleForm" label-width="100px" class="demo-ruleForm">
      <el-form-item label="商品类别" prop="categoryId">
        <el-input v-model="ruleForm.categoryId" placeholder="请输入商品类别的ID"></el-input>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="submitForm('ruleForm')">下一步</el-button>
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
        categoryId: '',
        categoryName: ''
      },
      rules: {
        categoryId: [
          {required: true, message: '请输入商品类别的ID', trigger: 'blur'},
          {pattern: /^\d+$/, message: '商品类别的ID必须是数字', trigger: 'blur'}
        ]
      }
    };
  },
  methods: {
    loadLocalRuleForm() {
      let localRuleFormString = localStorage.getItem('localRuleForm');
      let localRuleForm = JSON.parse(localRuleFormString);
      if (localRuleForm.categoryId) {
        this.ruleForm = localRuleForm;
      }
    },
    submitForm(formName) {
      this.$refs[formName].validate((valid) => {
        if (valid) {
          // 发请求，查询类别详情，判断类别是否存在，类别是否包含子级类别，如果均没有问题，则跳转到下一步
          let url = 'http://localhost:9080/categories/' + this.ruleForm.categoryId;
          console.log('url = ' + url);

          this.axios
              .create({'headers': {'Authorization': localStorage.getItem('jwt')}})
              .get(url).then((response) => {
            let responseBody = response.data;
            if (responseBody.state == 20000) {
              let category = responseBody.data;
              if (category.isParent == 1) {
                let message = '错误：选择的类别必须不包含子级类别！';
                this.$alert(message, '警告', {
                  confirmButtonText: '确定',
                  callback: action => {
                  }
                });
              } else {
                this.ruleForm.categoryName = category.name;
                localStorage.setItem('localRuleForm', JSON.stringify(this.ruleForm));
                this.$router.push('add-new-2');
              }
            } else {
              this.$alert(responseBody.message, '警告', {
                confirmButtonText: '确定',
                callback: action => {
                  this.loadAlbumList();
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
  }
}
</script>