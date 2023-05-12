<template>
  <div>
    <el-breadcrumb separator-class="el-icon-arrow-right" style="font-size: 16px;">
      <el-breadcrumb-item :to="{ path: '/sys-admin' }">
        <i class="el-icon-s-promotion"></i> 后台管理
      </el-breadcrumb-item>
      <el-breadcrumb-item>属性模板列表</el-breadcrumb-item>
    </el-breadcrumb>

    <el-divider></el-divider>

    <el-table :data="tableData" border style="width: 100%">
      <el-table-column prop="id" label="ID" width="60" align="center"></el-table-column>
      <el-table-column prop="name" label="名称" width="200" align="center"></el-table-column>
      <el-table-column prop="pinyin" label="拼音" width="200" align="center"></el-table-column>
      <el-table-column prop="keywords" label="关键词列表" header-align="center" :show-overflow-tooltip="true"></el-table-column>
      <el-table-column prop="sort" label="排序序号" width="80" align="center"></el-table-column>
      <el-table-column label="操作" width="100" align="center">
        <template slot-scope="scope">
          <el-button type="primary" icon="el-icon-edit" size="mini" circle
                     @click="openEditDialog(scope.row)"></el-button>
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
      tableData: []
    }
  },
  methods: {
    openEditDialog(attributeTemplate) {
      let title = '提示';
      let message = '您正在尝试编辑【' + attributeTemplate.id + '-' + attributeTemplate.name + '】的属性模板，抱歉，此功能尚未实现……';
      this.$alert(message, title, {
        confirmButtonText: '确定'
      });
    },
    openDeleteConfirm(attributeTemplate) {
      let title = '提示';
      let message = '此操作将永久删除【' + attributeTemplate.name + '】属性模板，是否继续？';
      this.$confirm(message, title, {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        this.handleDelete(attributeTemplate);
      }).catch(() => {
      });
    },
    handleDelete(attributeTemplate) {
      let url = 'http://localhost:9080/attribute-templates/' + attributeTemplate.id + '/delete';
      console.log('url = ' + url);

      this.axios
          .create({'headers': {'Authorization': localStorage.getItem('jwt')}})
          .post(url).then((response) => {
        let responseBody = response.data;
        if (responseBody.state == 20000) {
          this.loadAttributeTemplateList();
          this.$message({
            message: '删除属性模板【' + attributeTemplate.name + '】成功！',
            type: 'success'
          });
        } else {
          this.$alert(responseBody.message, '操作失败', {
            confirmButtonText: '确定',
            callback: action => {
              this.loadAttributeTemplateList();
            }
          });
        }
      });
    },
    loadAttributeTemplateList() {
      console.log('loadAttributeTemplateList...');
      let url = 'http://localhost:9080/attribute-templates';
      console.log('url = ' + url);

      this.axios
          .create({'headers': {'Authorization': localStorage.getItem('jwt')}})
          .get(url).then((response) => {
        let responseBody = response.data;
        this.tableData = responseBody.data;
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