<template>
  <div>
    <!-- 外层容器 -->
    <el-container>
      <!-- 上半区域：顶栏 -->
      <el-header class="layout-header">
        <h1>酷鲨商城运营管理平台</h1>
      </el-header>
      <!-- 下半区域 -->
      <el-container class="layout-body">
        <!-- 下半区域的左侧边栏 -->
        <el-aside class="layout-aside">
          <el-menu
              router
              :default-active="activeMenuItemPath"
              class="el-menu-vertical-demo"
              background-color="#222"
              text-color="#fff"
              active-text-color="#fff">
            <!-- 首页 -->
            <el-menu-item index="/sys-admin/index">
              <i class="el-icon-s-home"></i>
              <span>首页</span>
            </el-menu-item>
            <!-- 临时页面 -->
            <el-submenu index="1">
              <template slot="title">
                <i class="el-icon-s-tools"></i>
                <span>临时页面</span>
              </template>
              <el-menu-item index="/sys-admin/temp/admin/add-new">
                <i class="el-icon-circle-plus"></i>
                <span>添加管理员</span>
              </el-menu-item>
              <el-menu-item index="/sys-admin/temp/admin/list">
                <i class="el-icon-s-operation"></i>
                <span>管理员列表</span>
              </el-menu-item>
              <el-menu-item index="/sys-admin/temp/brand/add-new">
                <i class="el-icon-circle-plus"></i>
                <span>添加品牌</span>
              </el-menu-item>
              <el-menu-item index="/sys-admin/temp/brand/list">
                <i class="el-icon-s-operation"></i>
                <span>品牌列表</span>
              </el-menu-item>
              <el-menu-item index="/sys-admin/temp/category/add-new">
                <i class="el-icon-circle-plus"></i>
                <span>添加类别</span>
              </el-menu-item>
              <el-menu-item index="/sys-admin/temp/category/list">
                <i class="el-icon-s-operation"></i>
                <span>类别列表</span>
              </el-menu-item>
              <el-menu-item index="/sys-admin/temp/album/add-new">
                <i class="el-icon-circle-plus"></i>
                <span>添加相册</span>
              </el-menu-item>
              <el-menu-item index="/sys-admin/temp/album/list">
                <i class="el-icon-s-operation"></i>
                <span>相册列表</span>
              </el-menu-item>
              <el-menu-item index="/sys-admin/temp/attribute-template/add-new">
                <i class="el-icon-circle-plus"></i>
                <span>添加属性模板</span>
              </el-menu-item>
              <el-menu-item index="/sys-admin/temp/attribute-template/list">
                <i class="el-icon-s-operation"></i>
                <span>属性模板列表</span>
              </el-menu-item>
              <el-menu-item index="/sys-admin/temp/attribute/add-new">
                <i class="el-icon-circle-plus"></i>
                <span>添加属性</span>
              </el-menu-item>
              <el-menu-item index="/sys-admin/temp/attribute/list">
                <i class="el-icon-s-operation"></i>
                <span>属性列表</span>
              </el-menu-item>
            </el-submenu>
            <!-- 商品管理 -->
            <el-submenu index="3">
              <template slot="title">
                <i class="el-icon-s-goods"></i>
                <span>商品管理</span>
              </template>
              <el-menu-item index="3-1" disabled>
                <i class="el-icon-s-grid"></i>
                <span slot="title">SPU台账</span>
              </el-menu-item>
              <el-menu-item index="/sys-admin/product/spu/add-new-1">
                <i class="el-icon-circle-plus"></i>
                <span slot="title">新增SPU</span>
              </el-menu-item>
              <el-menu-item index="3-3" disabled>
                <i class="el-icon-picture"></i>
                <span slot="title">相册管理</span>
              </el-menu-item>
              <el-menu-item index="3-4" disabled>
                <i class="el-icon-s-unfold"></i>
                <span slot="title">商品类别</span>
              </el-menu-item>
              <el-menu-item index="3-5" disabled>
                <i class="el-icon-s-flag"></i>
                <span slot="title">属性模板</span>
              </el-menu-item>
              <el-menu-item index="3-6" disabled>
                <i class="el-icon-s-data"></i>
                <span slot="title">品牌管理</span>
              </el-menu-item>
            </el-submenu>
            <!-- 订单管理 -->
            <el-submenu index="4" disabled>
              <template slot="title">
                <i class="el-icon-s-order"></i>
                <span>订单管理</span>
              </template>
            </el-submenu>
            <!-- 营销管理 -->
            <el-submenu index="5" disabled>
              <template slot="title">
                <i class="el-icon-s-ticket"></i>
                <span>营销管理</span>
              </template>
            </el-submenu>
            <!-- 商家管理 -->
            <el-submenu index="6" disabled>
              <template slot="title">
                <i class="el-icon-s-shop"></i>
                <span>商家管理</span>
              </template>
            </el-submenu>
            <!-- 权限管理 -->
            <el-submenu index="7" disabled>
              <template slot="title">
                <i class="el-icon-s-check"></i>
                <span>权限管理</span>
              </template>
            </el-submenu>
          </el-menu>
        </el-aside>
        <!-- 下半区域的右侧主体 -->
        <el-main class="layout-main">
          <!-- 由其它视图来显示 -->
          <router-view/>
        </el-main>
      </el-container>
    </el-container>
  </div>
</template>

<style>
.layout-header {
  background: #1684b0;
}

.layout-header h1 {
  color: #fff;
  line-height: 60px;
}

.layout-body {
  position: absolute;
  top: 60px;
  bottom: 0;
  left: 0;
  right: 0;
}

.layout-aside {
  background: #222;
}

.layout-aside i {
  color: #fff !important;
}

.layout-main {
  background: #fff;
}

.el-menu-item.is-active {
  background: #2d3c4d !important;
}
</style>

<script>
export default {
  data() {
    return {
      activeMenuItemPath: ''
    }
  },
  mounted() {
    let path = this.$router.currentRoute.path;
    if (path.startsWith('/sys-admin/product/spu/add-new-')) {
      path = '/sys-admin/product/spu/add-new-1';
    }
    this.activeMenuItemPath = path;
  }
}
</script>