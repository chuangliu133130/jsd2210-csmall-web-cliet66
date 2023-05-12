<template>
  <div>
    <el-breadcrumb separator-class="el-icon-arrow-right" style="font-size: 16px;">
      <el-breadcrumb-item :to="{ path: '/' }">
        <i class="el-icon-s-promotion"></i> 后台管理
      </el-breadcrumb-item>
      <el-breadcrumb-item>商品管理</el-breadcrumb-item>
      <el-breadcrumb-item>新增SPU</el-breadcrumb-item>
      <el-breadcrumb-item>第3步：选择商品相册</el-breadcrumb-item>
    </el-breadcrumb>

    <el-divider></el-divider>

    <el-form :model="ruleForm" :rules="rules" ref="ruleForm" label-width="130px" class="demo-ruleForm">
      <el-form-item label="商品相册" prop="albumId">
        <el-select v-model="ruleForm.albumId" placeholder="请选择相册">
          <el-option
              v-for="item in albumListOptions"
              :key="item.id"
              :label="item.name"
              :value="item.id">
          </el-option>
        </el-select>
      </el-form-item>
      <el-form-item>
        <el-button type="warning" @click="backToPreStep()">上一步</el-button>
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
      albumListOptions: [],
      ruleForm: {
        albumId: '',
        albumName: ''
      },
      rules: {
        albumId: [
          {type: 'number', required: true, message: '请选择商品相册', trigger: 'blur'},
        ]
      }
    };
  },
  methods: {
    loadAlbumList() {
      let url = 'http://localhost:9080/albums';
      console.log('url = ' + url);

      this.axios
          .create({'headers': {'Authorization': localStorage.getItem('jwt')}})
          .get(url).then((response) => {
        let responseBody = response.data;
        this.albumListOptions = responseBody.data;
      });
    },
    backToPreStep() {
      this.$router.push('add-new-2');
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

      this.ruleForm = localRuleForm;
    },
    submitForm(formName) {
      this.$refs[formName].validate((valid) => {
        if (valid) {
          let url = 'http://localhost:9080/albums/' + this.ruleForm.albumId;
          console.log('url = ' + url);

          this.axios
              .create({'headers': {'Authorization': localStorage.getItem('jwt')}})
              .get(url).then((response) => {
            let responseBody = response.data;
            if (responseBody.state == 20000) {
              let album = responseBody.data;
              this.ruleForm.albumName = album.name;
              localStorage.setItem('localRuleForm', JSON.stringify(this.ruleForm));
              this.$router.push('add-new-4');
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
    this.loadAlbumList();
  }
}
</script>