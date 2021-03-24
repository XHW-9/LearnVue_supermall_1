<template>
  <div id="app">
    <keep-alive exclude="Detail">
      <router-view></router-view>
    </keep-alive>
    <main-tab-bar></main-tab-bar>
  </div>
</template>

<script>
  import MainTabBar from "@/components/content/mainTabbar/MainTabBar";
  import router from "@/router";
  export default {
    name: 'app',
    router,
    components: {
      MainTabBar,

    }
  }
  // this.$router.push(VueRouter).catch(err => {
  //   console.log('输出报错',err)
  // })
</script>

<style>
@import "./assets/css/base.css";
</style>
