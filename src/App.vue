<script setup>

</script>

<template>
  <el-container class="layout">
    <el-aside width="200px" class="aside">
      <div class="logo">管理系统</div>
      <el-menu router :default-active="$route.path">
        <el-menu-item index="/">
          <span>仪表盘</span>
        </el-menu-item>
        <el-menu-item index="/users">
          <span>用户管理</span>
        </el-menu-item>
      </el-menu>
    </el-aside>

  <el-container>
    <el-header class="header">后台管理系统</el-header>
    <el-main>
      <router-view />
    </el-main>
  </el-container>
  </el-container>
</template>

<style scoped>
.layout {
  height: 100vh;
}
.aside {
  background-color: #304156;
}
.logo {
  height: 60px;
  line-height: 60px;
  text-align: center;
  color: #fff;
  font-size: 18px;
  font-weight: bold;
}
.header {
  background-color: #fff;
  border-bottom: 1px solid #eee;
  display:flex;
  align-items: center;
  font-size: 18px;
}
</style>
