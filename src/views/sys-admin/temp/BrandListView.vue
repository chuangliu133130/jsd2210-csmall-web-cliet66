<template>
  <div>
    <el-breadcrumb separator-class="el-icon-arrow-right" style="font-size: 16px;">
      <el-breadcrumb-item :to="{ path: '/sys-admin' }">
        <i class="el-icon-s-promotion"></i> 后台管理
      </el-breadcrumb-item>
      <el-breadcrumb-item>品牌列表</el-breadcrumb-item>
    </el-breadcrumb>

    <el-divider></el-divider>

    <el-table :data="tableData" border style="width: 100%">
      <el-table-column prop="id" label="ID" width="60" align="center"></el-table-column>
      <el-table-column label="LOGO" width="70" align="center">
        <template slot-scope="scope">
          <el-avatar :size="30" :src="scope.row.logo" fit="contain"></el-avatar>
        </template>
      </el-table-column>
      <el-table-column prop="name" label="名称" width="120" align="center"></el-table-column>
      <el-table-column prop="pinyin" label="拼音" width="180" align="center"></el-table-column>
      <el-table-column prop="description" label="简介" header-align="center"
                       :show-overflow-tooltip="true"></el-table-column>
      <el-table-column prop="keywords" label="关键词列表" header-align="center"
                       :show-overflow-tooltip="true"></el-table-column>
      <el-table-column prop="sort" label="排序序号" width="80" align="center"></el-table-column>
      <el-table-column label="是否启用" width="80" align="center">
        <template slot-scope="scope">
          <el-switch
              @change="toggleEnable(scope.row)"
              v-model="scope.row.enable"
              :active-value="1"
              :inactive-value="0"
              active-color="#13ce66"
              inactive-color="#cccccc">
          </el-switch>
        </template>
      </el-table-column>
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
    toggleEnable(brand) {
      console.log('你点击了【' + brand.name + '】的开关控件，当前开关值：' + brand.enable);
      let enableText = ['禁用', '启用'];
      let url = 'http://localhost:9080/brands/' + brand.id;
      if (brand.enable == 1) {
        url += '/enable';
      } else {
        url += '/disable';
      }
      console.log('url = ' + url);

      this.axios
          .create({'headers': {'Authorization': localStorage.getItem('jwt')}})
          .post(url).then((response) => {
        let responseBody = response.data;
        if (responseBody.state == 20000) {
          let message = '将品牌【' + brand.name + '】的启用状态修改为【' + enableText[brand.enable] + '】成功！';
          this.$message({
            message: message,
            type: 'success'
          });
        } else {
          this.$message.error(responseBody.message);
        }
        if (responseBody.state == 40400) {
          this.loadBrandList();
        }
      });
    },
    openEditDialog(brand) {
      let title = '提示';
      let message = '您正在尝试编辑【' + brand.id + '-' + brand.name + '】的品牌详情，抱歉，此功能尚未实现……';
      this.$alert(message, title, {
        confirmButtonText: '确定'
      });
    },
    openDeleteConfirm(brand) {
      let title = '提示';
      let message = '此操作将永久删除【' + brand.name + '】品牌，是否继续？';
      this.$confirm(message, title, {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        this.handleDelete(brand);
      }).catch(() => {
      });
    },
    handleDelete(brand) {
      let url = 'http://localhost:9080/brands/' + brand.id + '/delete';
      console.log('url = ' + url);

      this.axios
          .create({'headers': {'Authorization': localStorage.getItem('jwt')}})
          .post(url).then((response) => {
        let responseBody = response.data;
        if (responseBody.state == 20000) {
          this.loadBrandList();
          this.$message({
            message: '删除品牌【' + brand.name + '】成功！',
            type: 'success'
          });
        } else {
          this.$alert(responseBody.message, '操作失败', {
            confirmButtonText: '确定',
            callback: action => {
              this.loadBrandList();
            }
          });
        }
      });
    },
    loadBrandList() {
      console.log('loadBrandList...');
      let url = 'http://localhost:9080/brands';
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
    this.loadBrandList();
  }
}
</script>

<style scoped>
</style>