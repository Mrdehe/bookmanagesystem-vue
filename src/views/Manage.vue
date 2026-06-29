<script setup lang="ts">
// 显示组件用法
</script>

<template>
  <!-- 头部开始-->
  <div style="height: 60px; background-color: lightgrey">
  </div>
  <!-- 头部结束-->

  <!-- 主体开始-->
  <div style="display: flex">
    <!-- 主体左侧导航区域开始-->
    <div style="width: 200px; border-right: 1px solid #dddddd; min-height: calc(100vh - 60px)">
      <el-menu
          default-active="2"
          class="el-menu-vertical-demo"
          router
      >
        <el-menu-item index="/manage/home">系统首页</el-menu-item>
        <el-sub-menu index="1">
          <template #title>
            <el-icon><location /></el-icon>
            <span>用户管理</span>
          </template>
          <el-menu-item index="1-1">管理员信息</el-menu-item>
          <el-menu-item index="1-2">普通用户信息</el-menu-item>
        </el-sub-menu>
        <el-menu-item index="/manage/book">图书管理</el-menu-item>
      </el-menu>
    </div>
    <!-- 主体左侧导航区域结束-->

    <!-- 主体右侧数据区域开始-->
    <div>
      <RouterView></RouterView>
    </div>
    <!-- 主体右侧数据区域结束-->
  </div>
  <!-- 主体结束-->

  <!--  <div>-->
  <!--    父级页面，可以嵌套其他的子路由页面-->
  <!--  </div>-->
  <!--  <div style="width: 50%; background-color: azure; padding: 30px; border: 2px solid #ccc; margin: 30px auto">-->
  <!--    <RouterView></RouterView>-->
  <!--  </div>-->
</template>

<style scoped>

</style>