<template>
  <div>
    <h1>我的复机单</h1>
    <el-tag type='info'>申请或者修改的复机单</el-tag> <br>
    <!-- <el-tabs v-model="activeName" @tab-click="handleClick">
      <el-tab-pane label="我的申请" name="start">
      </el-tab-pane>
      <el-tab-pane label="我的修改" name="mod">
      </el-tab-pane>
    </el-tabs> -->
    <br>
    <el-table
      :data="orders"
      style="width: 100%"
      border
      @row-click='rowClick'
      header-row-class-name='table-header'
    >
      <el-table-column label="编号" min-width='150'>
        <template slot-scope="scope">
          <router-link
            :to="'/tft/order/detail/' + scope.row.order.id"
            class='id-href'
          >
            {{ scope.row.order.id }}
          </router-link>
        </template>
      </el-table-column>
      <el-table-column prop="order.status" label="状态" min-width='180'></el-table-column>
      <el-table-column prop="id" label="序号" min-width='60'></el-table-column>
      <el-table-column prop='user.realname' label="申请人" min-width='80'></el-table-column>
      <el-table-column prop='created' label="申请时间" :formatter='formatDate' min-width='150'></el-table-column>
      <el-table-column prop='mod_user.realname' label="修改人" min-width='80'></el-table-column>
      <el-table-column prop='modified' label="修改时间" :formatter='formatDate' min-width='150'></el-table-column>
      <el-table-column prop='solution' label="责任单位对策说明" min-width='150' show-overflow-tooltip></el-table-column>
      <el-table-column prop='explain' label="先行 lot 结果说明" min-width='150' show-overflow-tooltip></el-table-column>
      <el-table-column label="部分复机" min-width='100'>
        <template slot-scope="scope">
          <span v-if='scope.row.partial'>是</span>
          <span v-else>否</span>
        </template>
      </el-table-column>
    </el-table>
    <el-pagination
      background
      @current-change="handleCurrentChange"
      :current-page.sync="page"
      :page-size='pageSize'
      layout="prev, pager, next, jumper"
      :total="count">
    </el-pagination>
  </div>
</template>

<script>
import { mapGetters } from 'vuex'
import { formatDate } from '@/common/js/date.js'
import { getRecoverOrders } from '@/api/tft'

export default {
  name: 'MyRecover',
  data () {
    return {
      page: 1,
      pageSize: 15,
      count: null,
      orders: []
    }
  },
  computed: {
    ...mapGetters({
      username: 'username'
    })
  },
  methods: {
    // handleClick (tab, event) {
    //   this.page = 1
    //   if (tab.name === 'start') {
    //     this.getStartOrders()
    //   } else if (tab.name === 'mod') {
    //     this.getModOrders()
    //   }
    // },
    getOrders () {
      getRecoverOrders({page: this.page, 'page-size': this.pageSize, name: this.username})
        .then((res) => {
          this.count = res.data.count
          this.orders = res.data.results
        })
        .catch((error) => {
          console.log(error)
        })
    },
    // getStartOrders () {
    //   getOrders({page: this.page, 'page-size': this.pageSize, r_user: this.username})
    //     .then((res) => {
    //       this.count = res.data.count
    //       this.orders = res.data.results
    //     })
    //     .catch((error) => {
    //       console.log(error)
    //     })
    // },
    // getModOrders () {
    //   getOrders({page: this.page, 'page-size': this.pageSize, r_mod: this.username})
    //     .then((res) => {
    //       this.count = res.data.count
    //       this.orders = res.data.results
    //     })
    //     .catch((error) => {
    //       console.log(error)
    //     })
    // },
    // tableRowClassName ({row, rowIndex}) {
    //   if (row.status.code === '0') {
    //     return 'status0'
    //   } else if (row.status.code === '1') {
    //     return 'status1'
    //   } else if (row.status.code === '2') {
    //     return 'status2'
    //   } else if (row.status.code === '3') {
    //     return 'status3'
    //   } else if (row.status.code === '4') {
    //     return 'status4'
    //   } else if (row.status.code === '5') {
    //     return 'status5'
    //   } else if (row.status.code === '6') {
    //     return 'status6'
    //   } else if (row.status.code === '7') {
    //     return 'status7'
    //   } else if (row.status.code === '8') {
    //     return 'status8'
    //   } else if (row.status.code === '9') {
    //     return 'status9'
    //   }
    //   return 'status0'
    // },
    handleCurrentChange (val) {
      this.getOrders()
    },
    formatDate (row, column, time, index) {
      let date = new Date(time)
      return formatDate(date, 'yyyy-MM-dd hh:mm:ss')
    },
    rowClick (row, event, column) {
      this.$router.push({path: `/tft/order/detail/${row.id}`})
    }
  },
  filters: {
    formatDate (time) {
      let date = new Date(time)
      return formatDate(date, 'yyyy-MM-dd hh:mm:ss')
    }
  },
  mounted () {
    this.getOrders()
  }
}
</script>

<style lang="stylus">
@import '~styles/varibles'
.table-header
  th
    font-size 1.1em
    background #CFD5DA
.id-href
  text-decoration none
  color #22558B
.el-table
  td
    div
      max-height 2.2em
      // overflow hidden
      // white-space nowrap
      // text-overflow ellipsis
  .status0
    background $status0
  .status1
    background $status1
  .status2
    background $status2
  .status3
    background $status3
  .status4
    background $status4
  .status5
    background $status5
  .status6
    background $status6
  .status7
    background $status7
  .status8
    background $status8
  .status9
    background $status9
.status0:hover
.status1:hover
.status2:hover
.status3:hover
.status4:hover
.status5:hover
.status6:hover
.status7:hover
.status8:hover
.status9:hover
  font-weight bold
.el-pagination
  margin 20px 0
</style>
