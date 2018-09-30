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
  data () {
    return {
      // lasttime: new Date().getTime()
      lasttime: parseInt(localStorage.getItem('lasttime'))
    }
  },
  components: {
    HomeHeader
  },
  methods: {
    beforeunloadHandler () {
      // 区分关闭还是刷新
      // 鼠标相对于用户屏幕的水平位置 - 窗口左上角相对于屏幕左上角的水平位置 = 鼠标在当前窗口上的水平位置
      // let n = window.event.screenX - window.screenLeft
      // // 鼠标在当前窗口内时，n<m，b为false；鼠标在当前窗口外时，n>m，b为true。20这个值是指关闭按钮的宽度
      // let b = n > document.documentElement.scrollWidth - 20
      // if (b && window.event.clientY < 0 || window.event.altKey || window.event.ctrlKey) {
      //   console.log('close')
      // } else if (window.event.clientY > document.body.clientHeight || window.event.altKey) {
      //   console.log('刷新')
      // }
      localStorage.setItem('lasttime', this.lasttime)
    },
    mousemoveHandler () {
      this.lasttime = new Date().getTime()
    }
  },
  mounted () {
    window.addEventListener('beforeunload', this.beforeunloadHandler)
    window.addEventListener('mousemove', this.mousemoveHandler)

    let current = new Date().getTime()
    let delta = current - this.lasttime
    if (isNaN(delta) || delta > 600000) {
      localStorage.removeItem('token')
      localStorage.removeItem('username')
      this.$store.commit('setInfo')
      this.$router.push({name: 'Home'})
    }

    let _this = this
    this.timer = setInterval(() => {
      let current = new Date().getTime()
      if (current - _this.lasttime > 600000) {
        localStorage.removeItem('token')
        localStorage.removeItem('username')
        // 记得加上啊，更新全局状态
        this.$store.commit('setInfo')
        this.$router.push({name: 'Home'})
      }
    }, 300000)
  },
  destroyed () {
    window.removeEventListener('mousemove', this.mousemoveHandler)
    window.removeEventListener('beforeunload', this.beforeunloadHandler)
    if (this.timer) {
      clearInterval(this.timer) // 在vue实例销毁钱，清除我们的定时器
    }
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
