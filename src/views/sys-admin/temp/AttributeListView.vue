<template>
  <div>
    <el-breadcrumb separator-class="el-icon-arrow-right" style="font-size: 16px;">
      <el-breadcrumb-item :to="{ path: '/sys-admin' }">
        <i class="el-icon-s-promotion"></i> 后台管理
      </el-breadcrumb-item>
      <el-breadcrumb-item>属性列表</el-breadcrumb-item>
    </el-breadcrumb>

    <el-divider></el-divider>

    <el-form style="margin: 0 0 10px 0;" :model="ruleForm" ref="ruleForm" label-width="130px" class="demo-ruleForm">
      <el-select v-model="ruleForm.templateId" placeholder="请选择属性模板"
        @change="loadAttributeList()">
        <el-option
            v-for="item in attributeTemplateListOptions"
            :key="item.id"
            :label="item.name"
            :value="item.id">
        </el-option>
      </el-select>
    </el-form>

    <el-table :data="tableData" border style="width: 100%">
      <el-table-column prop="id" label="ID" width="60" align="center"></el-table-column>
      <el-table-column prop="name" label="名称" width="120" align="center"></el-table-column>
      <el-table-column prop="description" label="简介" align="center"></el-table-column>
      <el-table-column label="类型" width="100" align="center">
        <template slot-scope="scope">
          <span v-text="typeText[scope.row.type]"></span>
        </template>
      </el-table-column>
      <el-table-column label="输入类型" width="100" align="center">
        <template slot-scope="scope">
          <span v-text="inputTypeText[scope.row.inputType]"></span>
        </template>
      </el-table-column>
      <el-table-column prop="valueList" label="备选值列表" width="100" align="center"></el-table-column>
      <el-table-column prop="unit" label="计量单位" width="80" align="center"></el-table-column>
      <el-table-column prop="sort" label="排序序号" width="80" align="center"></el-table-column>
      <el-table-column label="操作" width="100" align="center">
        <template slot-scope="scope">
          <el-button type="primary" icon="el-icon-edit" size="mini" circle
                     @click="handleEdit(scope.row)"></el-button>
          <el-button type="danger" icon="el-icon-delete" size="mini" circle
                     @click="openDeleteConfirm(scope.row)"></el-button>
        </template>
      </el-table-column>
    </el-table>
  </div>
</template>

<script>
export default {
  data() {
    return {
      attributeTemplateListOptions: [],
      ruleForm: {
        templateId: ''
      },
      typeText: ['非销售属性', '销售属性'],
      inputTypeText: ['手动录入', '单选', '多选', '单选（下拉列表）', '多选（下拉列表）'],
      tableData: []
    }
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
    loadAttributeList() {
      let url = 'http://localhost:9080/attributes/list-by-template?templateId='
          + this.ruleForm.templateId;
      console.log('url = ' + url);
      this.axios
          .create({'headers': {'Authorization': localStorage.getItem('jwt')}})
          .get(url).then((response) => {
        let responseBody = response.data;
        if (responseBody.state == 20000) {
          this.tableData = responseBody.data;
        } else {
          this.$message.error(responseBody.message);
        }
      });
    },
    handleEdit(attribute) {
      let title = '提示';
      let message = '您正在尝试编辑【' + attribute.id + '-' + attribute.name + '】的属性，抱歉，此功能尚未实现……';
      this.$alert(message, title, {
        confirmButtonText: '确定'
      });
    },
    openDeleteConfirm(attribute) {
      let title = '提示';
      let message = '此操作将永久删除【' + attribute.name + '】属性, 是否继续?';
      this.$confirm(message, title, {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        this.handleDelete(attribute);
      }).catch(() => {
      });
    },
    handleDelete(attribute) {
      let url = 'http://localhost:9080/attributes/' + attribute.id + '/delete';
      console.log('url = ' + url);
      this.axios
          .create({'headers': {'Authorization': localStorage.getItem('jwt')}})
          .post(url).then((response) => {
        let responseBody = response.data;
        if (responseBody.state == 20000) {
          this.$message({
            message: '删除属性【' + attribute.name + '】成功！',
            type: 'success'
          });
          this.loadAttributeList();
        } else {
          this.$alert(responseBody.message, '警告', {
            confirmButtonText: '确定',
            callback: action => {
              this.loadAttributeList();
            }
          });
        }
      });
    }
  },
  mounted() {
    this.loadAttributeTemplateList();
  }
}
</script>

<style scoped>
</style>