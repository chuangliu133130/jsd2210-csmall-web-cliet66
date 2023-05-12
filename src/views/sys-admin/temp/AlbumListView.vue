<template>
  <div>
    <el-breadcrumb separator-class="el-icon-arrow-right" style="font-size: 16px;">
      <el-breadcrumb-item :to="{ path: '/' }">
        <i class="el-icon-s-promotion"></i> 后台管理
      </el-breadcrumb-item>
      <el-breadcrumb-item>相册列表</el-breadcrumb-item>
    </el-breadcrumb>

    <el-divider></el-divider>

    <el-table :data="tableData" border style="width: 100%">
      <el-table-column prop="id" label="ID" width="80" align="center"></el-table-column>
      <el-table-column prop="name" label="名称" width="300" header-align="center"
                       :show-overflow-tooltip="true"></el-table-column>
      <el-table-column prop="description" label="简介" header-align="center"
                       :show-overflow-tooltip="true"></el-table-column>
      <el-table-column prop="sort" label="排序序号" width="100" align="center"></el-table-column>
      <el-table-column label="操作" width="100" align="center">
        <template slot-scope="scope">
          <el-button type="primary" icon="el-icon-edit" circle size="mini"
                     @click="openEditDialog(scope.row)"></el-button>
          <el-button type="danger" icon="el-icon-delete" circle size="mini"
                     @click="openDeleteConfirm(scope.row)"></el-button>
        </template>
      </el-table-column>
    </el-table>

    <!-- 以下是弹出的修改相册的对话框 -->
    <el-dialog title="编辑相册" :visible.sync="dialogFormVisible">
      <el-form :model="editForm">
        <el-form-item label="名称" :label-width="formLabelWidth">
          <el-input v-model="editForm.name" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="简介" :label-width="formLabelWidth">
          <el-input v-model="editForm.description" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="排序序号" :label-width="formLabelWidth">
          <el-input v-model="editForm.sort" autocomplete="off"></el-input>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="dialogFormVisible = false">取 消</el-button>
        <el-button type="primary" @click="submitEditForm()">确 定</el-button>
      </div>
    </el-dialog>
  </div>
</template>

<script>
export default {
  data() {
    return {
      tableData: [],
      dialogFormVisible: false,
      editForm: {
        id: '',
        name: '',
        description: '',
        sort: ''
      },
      formLabelWidth: '120px'
    }
  },
  methods: {
    openEditDialog(album) {
      let url = 'http://localhost:9080/albums/' + album.id;
      console.log('url = ' + url);

      this.axios
          .create({'headers': {'Authorization': localStorage.getItem('jwt')}})
          .get(url).then((response) => {
        let responseBody = response.data;
        if (responseBody.state == 20000) {
          this.dialogFormVisible = true;
          this.editForm = responseBody.data;
        } else {
          this.$alert(responseBody.message, '警告', {
            confirmButtonText: '确定',
            callback: action => {
              this.loadAlbumList();
            }
          });
        }
      });
    },
    submitEditForm() {
      let url = 'http://localhost:9080/albums/' + this.editForm.id + '/update';
      console.log('url = ' + url);
      let formData = this.qs.stringify(this.editForm);
      console.log('formData = ' + formData);

      this.axios
          .create({'headers': {'Authorization': localStorage.getItem('jwt')}})
          .post(url, formData).then((response) => {
        let responseBody = response.data;
        if (responseBody.state == 20000) {
          this.$message({
            message: '修改相册成功！',
            type: 'success'
          });
          this.loadAlbumList();
          this.dialogFormVisible = false;
        } else if (responseBody.state == 40400) {
          this.$alert(responseBody.message, '警告', {
            confirmButtonText: '确定',
            callback: action => {
              this.dialogFormVisible = false;
              this.loadAlbumList();
            }
          });
        } else {
          this.$message.error(responseBody.message);
        }
      });
    },
    openDeleteConfirm(album) {
      let message = '此操作将永久删除【' + album.name + '】相册，是否继续？';
      this.$confirm(message, '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        this.handleDelete(album);
      }).catch(() => {
      });
    },
    handleDelete(album) {
      let url = 'http://localhost:9080/albums/' + album.id + '/delete';
      console.log('url = ' + url);

      this.axios
          .create({'headers': {'Authorization': localStorage.getItem('jwt')}})
          .post(url).then((response) => {
        let responseBody = response.data;
        if (responseBody.state == 20000) {
          this.$message({
            message: '删除相册成功！',
            type: 'success'
          });
          this.loadAlbumList();
        } else if (responseBody.state == 40400) {
          this.$alert(responseBody.message, '警告', {
            confirmButtonText: '确定',
            callback: action => {
              this.loadAlbumList();
            }
          });
        } else {
          this.$message.error(responseBody.message);
        }
      });
    },
    loadAlbumList() {
      let url = 'http://localhost:9080/albums';
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
    this.loadAlbumList();
  }
}
</script>

<style scoped>

</style>