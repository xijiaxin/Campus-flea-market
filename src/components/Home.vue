<template>
  <el-container class="home-container">
    <!-- 头部区域 -->
  <el-header >
    <div>
      <img src="../assets/Oylogo.png" height="50"  alt="">
      <span>校园二手交易平台后台管理系统</span>
    </div>
    <!--通过清除token实现退出-->
    <el-button type="info" @click="logout">退出</el-button>
    </el-header>
    <!-- 页面主体区域 -->
  <el-container>
    <!-- 侧边栏区域 -->
    <el-aside :width="isCollapse ? '64px' : '200px'">
      <div class="toggle-button" @click="toggloCollapse">|||</div>
      <!-- 侧边栏菜单区域 -->
      <el-menu
      background-color="#25B7AA"
      text-color="#fff"
      active-text-color="#CD5C5C"
      unique-opened :collapse="isCollapse"
      :collapse-transition="false" router
      >
      <!-- 一级菜单 -->
      <!-- 用户管理 -->
      <el-submenu index="1">
        <!-- 一级菜单模板区域 -->
        <template slot="title">
          <!-- 图标 -->
          <i class="el-icon-user-solid"></i>
          <!-- 文本 -->
          <span>用户管理</span>
        </template>
        <!-- 二级菜单 -->
        <el-menu-item index="/users">
           <!-- 二级菜单模板区域 -->
        <template slot="title">
          <!-- 图标 -->
          <i class="el-icon-menu"></i>
          <!-- 文本 -->
          <span>用户列表</span>
        </template>
        </el-menu-item>
      </el-submenu>
      <!-- 权限管理 -->
      <el-submenu index="2">
        <!-- 一级菜单模板区域 -->
        <template slot="title">
          <!-- 图标 -->
          <i class="el-icon-s-tools"></i>
          <!-- 文本 -->
          <span>权限管理</span>
        </template>
        <!-- 二级菜单 -->
        <el-menu-item index="/roles">
           <!-- 二级菜单模板区域 -->
        <template slot="title">
          <!-- 图标 -->
          <i class="el-icon-menu"></i>
          <!-- 文本 -->
          <span>角色列表</span>
        </template>
        </el-menu-item>
        <el-menu-item index="/rights">
           <!-- 二级菜单模板区域 -->
        <template slot="title">
          <!-- 图标 -->
          <i class="el-icon-menu"></i>
          <!-- 文本 -->
          <span>权限列表</span>
        </template>
        </el-menu-item>
      </el-submenu>
      <!-- 商品管理 -->
      <el-submenu index="3">
        <!-- 一级菜单模板区域 -->
        <template slot="title">
          <!-- 图标 -->
          <i class="el-icon-s-goods"></i>
          <!-- 文本 -->
          <span>商品管理</span>
        </template>
        <!-- 二级菜单 -->
        <el-menu-item index="3-1">
           <!-- 二级菜单模板区域 -->
        <template slot="title">
          <!-- 图标 -->
          <i class="el-icon-menu"></i>
          <!-- 文本 -->
          <span>商品列表</span>
        </template>
        </el-menu-item>
         <!-- 二级菜单 -->
        <el-menu-item index="3-2">
           <!-- 二级菜单模板区域 -->
        <template slot="title">
          <!-- 图标 -->
          <i class="el-icon-menu"></i>
          <!-- 文本 -->
          <span>分类参数</span>
        </template>
        </el-menu-item>
         <!-- 二级菜单 -->
        <el-menu-item index="3-3">
           <!-- 二级菜单模板区域 -->
        <template slot="title">
          <!-- 图标 -->
          <i class="el-icon-menu"></i>
          <!-- 文本 -->
          <span>商品分类</span>
        </template>
        </el-menu-item>
      </el-submenu>
      <!-- 订单管理 -->
      <el-submenu index="4">
        <!-- 一级菜单模板区域 -->
        <template slot="title">
          <!-- 图标 -->
          <i class="el-icon-s-order"></i>
          <!-- 文本 -->
          <span>订单管理</span>
        </template>
        <!-- 二级菜单 -->
        <el-menu-item index="1-4-1">
           <!-- 二级菜单模板区域 -->
        <template slot="title">
          <!-- 图标 -->
          <i class="el-icon-menu"></i>
          <!-- 文本 -->
          <span>订单列表</span>
        </template>
        </el-menu-item>
      </el-submenu>
      <!-- 数据统计 -->
      <el-submenu index="5">
        <!-- 一级菜单模板区域 -->
        <template slot="title">
          <!-- 图标 -->
          <i class="el-icon-s-marketing"></i>
          <!-- 文本 -->
          <span>数据统计</span>
        </template>
        <!-- 二级菜单 -->
        <el-menu-item index="1-4-1">
           <!-- 二级菜单模板区域 -->
        <template slot="title">
          <!-- 图标 -->
          <i class="el-icon-menu"></i>
          <!-- 文本 -->
          <span>导航一</span>
        </template>
        </el-menu-item>
      </el-submenu>
      <!-- 分类管理 -->
      <el-submenu index="6">
        <!-- 一级菜单模板区域 -->
        <template slot="title">
          <!-- 图标 -->
          <i class="el-icon-user-solid"></i>
          <!-- 文本 -->
          <span>分类管理</span>
        </template>
        <!-- 二级菜单 -->
        <el-menu-item index="/management">
           <!-- 二级菜单模板区域 -->
        <template slot="title">
          <!-- 图标 -->
          <i class="el-icon-menu"></i>
          <!-- 文本 -->
          <span>分类列表</span>
        </template>
        </el-menu-item>
      </el-submenu>
    </el-menu>
    </el-aside>
    <!-- 主体框架区域 -->
    <el-main>
      <router-view></router-view>
    </el-main>
  </el-container>
</el-container>
</template>

<script>
export default {
  data() {
    return {
      isCollapse: false
    }
  },
  methods: {
    logout() {
      // 通过清除token实现退出
      window.sessionStorage.clear()
      this.$router.push('/login')
    },
    // 点击按钮切换菜单的折叠与展开
    toggloCollapse() {
      this.isCollapse = !this.isCollapse
    }
  }
}
</script>

<style lang="less" scoped>
.home-container{
  height: 100%;
}
.el-header{
  background-color: #3A445C;
  display: flex;
  justify-content: space-between;
  padding-left: 0%;
  align-items: center;
  color: #fff;
  font-size: 40px;
  > div{
    display: flex;
    align-items: center;
    span{
      margin-left: 25px;
    }
  }
}
.el-aside{
  background-color: #25B7AA;
  .el-menu{
    border-right: none;
  }
}
.el-main{
  background-color: #eaedeaf1;
}
.toggle-button{
  background-color: #4A5064;
  font-size: 10px;
  line-height: 23px;
  color: #fff;
  text-align: center;
  letter-spacing: 0.2em;
  cursor:pointer;
}
</style>
