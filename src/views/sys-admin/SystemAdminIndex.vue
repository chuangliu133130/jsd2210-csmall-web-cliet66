<template>
  <div>
    <el-breadcrumb separator-class="el-icon-arrow-right" style="font-size: 16px;">
      <el-breadcrumb-item :to="{ path: '/' }">
        <i class="el-icon-s-promotion"></i> 后台管理
      </el-breadcrumb-item>
      <el-breadcrumb-item>首页</el-breadcrumb-item>
    </el-breadcrumb>

    <el-divider></el-divider>

    <el-empty description="当前页面正在开发中，敬请期待……"></el-empty>
  </div>
</template>

<script>
export default {
  name: "SystemAdminIndex"
}
</script>

<style scoped>

</style>