<template>
  <el-container>
    <el-header>
      <HomeHeader></HomeHeader>
    </el-header>
    <router-view/>
    <el-footer>
      Copyright © 2010-2020, Cecpandalcd, All Rights Reserved.
    </el-footer>
  </el-container>
</template>

<script>
import HomeHeader from './home/Header'

export default {
  name: 'Home',
  // data () {
  //   return {
  //     lasttime: new Date.getTime()
  //   }
  // }
  components: {
    HomeHeader
  },
  methods: {
    beforeunloadHandler () {
      localStorage.removeItem('token')
      localStorage.removeItem('username')
    }
  },
  mounted () {
    window.addEventListener('beforeunload', this.beforeunloadHandler)
    // window.addEventListener('mousemove', this.mousemovedHandler)
    // let _this = this
    // this.timer = setInterval(() => {
    // }, 1000)
  },
  destroyed () {
    window.removeEventListener('beforeunload', this.beforeunloadHandler)
  }
}
</script>

<style lang='stylus' scoped>
.el-header
  color: #333
  text-align center
  line-height 60px
  position relativce
  margin-bottom 20px
.el-footer
  height 60px
  line-height 60px
  text-align center
  font-size 15px
  // margin-top 3px
  // border 1px solid green
  // background-color #A9AEA6
// .el-main
//   background-color: #E9EEF3
//   color: #333
//   text-align: center
//   line-height: 160px
//   border 1px solid red
// .el-aside
//   background-color: #D3DCE6
//   color: #333
//   text-align: center
//   line-height: 200px
//   border 1px solid red
</style>
