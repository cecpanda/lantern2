<template>
  <el-container>
    <el-aside width="">
      <el-menu
        default-active="1"
        class="el-menu-vertical-demo"
        @open="handleOpen"
        @close="handleClose"
        :collapse="isCollapse"
        :router='true'
      >
        <el-submenu index="1">
          <template slot="title">
            <i class='el-icon-menu'></i>
            <span slot="title">我的账号</span>
          </template>
          <el-menu-item index="/user/edit">修改资料</el-menu-item>
          <el-menu-item index="/user/change-password">修改密码</el-menu-item>
        </el-submenu>
        <el-menu-item index="/user/follow">
          <i class='el-icon-star-on'></i>
          <span slot="title">我的关注</span>
        </el-menu-item>
        <el-menu-item index="4">
          <i class="el-icon-setting"></i>
          <span slot="title">个人设置</span>
        </el-menu-item>
        <el-radio-group v-model="isCollapse" style="margin-bottom: 20px;">
          <el-radio-button :label="false" v-if='isCollapse'>
            <i class='el-icon-arrow-right'></i>
          </el-radio-button>
          <el-radio-button :label="true" v-else>
            <i class='el-icon-d-arrow-left'></i>
          </el-radio-button>
        </el-radio-group>
      </el-menu>
    </el-aside>
    <el-main>
      <router-view></router-view>
    </el-main>
<!--     <el-aside>
      Aside
    </el-aside> -->
  </el-container>
</template>

<script>
export default {
  name: 'User',
  data () {
    return {
      isCollapse: true,
      profile: {}
    }
  },
  methods: {
    handleOpen (key, keyPath) {
      console.log(key, keyPath)
    },
    handleClose (key, keyPath) {
      console.log(key, keyPath)
    }
  }
}
</script>

<style lang='stylus' scoped>
.el-menu-vertical-demo:not(.el-menu--collapse)
  width: 200px
  min-height: 400px
.iconfont
  padding 10px auto
</style>
