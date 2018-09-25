<template>
  <el-popover
    placement="top-start"
    :title="username"
    width="250"
    trigger="hover"
  >
    <img :src="user.avatar" alt='avatar' class='avatar'>
    {{ user.realname }} <br>
    {{ user.email }} <br>
    {{ user.mobile }} <br>
    {{ user.phone }} <br>
    <span v-if='user.groups.length'>{{ user.groups[0].name }}</span>
    <br>
    <br>
    注册日期: {{ user.date_joined | formatDate }}
    <el-button slot="reference">{{ username }}</el-button>
  </el-popover>
</template>

<script>
import { formatDate } from '@/common/js/date.js'
import { getUser } from '@/api/user'

export default {
  name: 'UserPopover',
  props: {
    username: {
      type: String,
      required: true
    }
  },
  data () {
    return {
      user: {
        groups: []
      }
    }
  },
  methods: {
    getUser () {
      getUser(this.username)
        .then((res) => {
          this.user = res.data
        })
        .catch((err) => {
          console.log(err)
        })
    }
  },
  filters: {
    formatDate (time) {
      let date = new Date(time)
      return formatDate(date, 'yyyy-MM-dd hh:mm:ss')
    }
  },
  mounted () {
    this.getUser()
  }
}
</script>

<style lang='stylus'>
.el-popover
  background-color #D9DDF4
  opacity 0.9
.avatar
  display block
  width 150px
  height 150px
  float right
</style>
