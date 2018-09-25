<template>
  <el-main>
    <!-- <img class='top' src="../assets/top.jpg" alt="top">
    <img class='logo' src="../assets/logo.png" alt="logo"> -->
    <h1>停机管理系统</h1>
  </el-main>
  <!-- <el-container>
    <el-main>
      <img class='top' src="../assets/top.jpg" alt="top">
      <img class='logo' src="../assets/logo.png" alt="logo">
    </el-main>
    <el-aside width="200px">
      aside
    </el-aside>
  </el-container> -->
</template>

<script>
import HomeHeader from './Header'

export default {
  name: 'Home',
  components: {
    HomeHeader
  }
}
</script>

<style lang='stylus' scoped>
.el-main
  height 650px
  background #B0BECD url('../assets/bg.jpg')
  background-size 100% 100%
  opacity 0.5
  // .top
  //   position absolute
  //   top 70px
  //   left 0
  //   width 25%
  // .logo
  //   position absolute
  //   top 100px
  //   right 3%
  h1
    position absolute
    top 50px
    left 3%
    font-size 60px
    color #9FAFD9
    text-shadow: 5px 5px 5px #000
// .el-aside
//   background-color: #D3DCE6
//   color: #333
//   text-align: center
</style>
