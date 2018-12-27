<template>
  <div class='query'>
    <h1>报表查询</h1>
    <el-row class="search">
      <el-col :span='8'>
        <el-input
          placeholder="开单人或修改人的工号"
          prefix-icon="el-icon-search"
          v-model="username">
          <span slot='prepend'>搜用户名</span>
        </el-input>
        <br><br>
        <el-input
          placeholder="开单人或修改人的真名"
          prefix-icon="el-icon-search"
          v-model="realname">
          <span slot='prepend'>搜索真名</span>
        </el-input>
        <br><br>
        <!-- <span class='search-label'>订单状态</span>
        <el-select
          v-model="status"
          clearable
          placeholder="请选择订单状态">
          <el-option
            v-for="item in statusOptions"
            :key="item.value"
            :label="item.label"
            :value="item.value">
          </el-option>
        </el-select>
        <br><br> -->
        <span class='search-label'>订单状态</span>
        <el-select
          v-model="status"
          clearable
          multiple
          placeholder="请选择订单状态">
          <el-option
            v-for="item in statusOptions"
            :key="item.value"
            :label="item.label"
            :value="item.value">
          </el-option>
        </el-select>
        <br><br>
        <span class='search-label'>开单工程</span>
        <el-select
          v-model="group"
          clearable
          multiple
          placeholder="请选择开单工程">
          <el-option
            v-for="item in groupOptions"
            :key="item.value"
            :label="item.label"
            :value="item.value">
          </el-option>
        </el-select>
        <br><br>
        <span class='search-label'>责任工程</span>
        <el-select
          v-model="charge_group"
          clearable
          multiple
          placeholder="请选择责任工程">
          <el-option
            v-for="item in groupOptions"
            :key="item.value"
            :label="item.label"
            :value="item.value">
          </el-option>
        </el-select>
        <br><br>
        <el-input
          placeholder="忽略大小写，模糊匹配"
          prefix-icon="el-icon-search"
          v-model="eq">
          <span slot='prepend'>停机设备</span>
        </el-input>
        <br><br>
        <el-input
          placeholder="请输入搜索内容"
          prefix-icon="el-icon-search"
          v-model="search">
          <span slot='prepend'>全局搜索</span>
        </el-input>
        <br><br>
        <el-date-picker
          v-model="created"
          type="datetimerange"
          :picker-options="pickerClock"
          range-separator="至"
          start-placeholder="开单开始日期"
          end-placeholder="开单结束日期"
          align="left">
        </el-date-picker>
        <el-button icon="el-icon-message" type="info" @click='handleTips'>说明</el-button>
        <el-button icon="el-icon-search" type="primary" @click='handleSearch'>搜索</el-button>
      </el-col>
      <el-col :span='10' :offset='2' v-if='searchFlag' class='search-content'>
        <i class="el-icon-info" v-if='params.username'>
          用户名: {{ params.username }}
        </i><br>
        <i class="el-icon-info" v-if='params.realname'>
          真名: {{ params.realname }}
        </i><br>
        <i class="el-icon-info" v-for='index of params.status' :key='index'>
          订单状态: {{ statusOptions[index - 1].label }}
        </i><br>
        <i class="el-icon-info">
          开单工程: <span v-for='group of params.group' :key='group'>{{ group }} </span>
        </i><br>
        <i class="el-icon-info">
          责任工程: <span v-for='group of params.charge_group' :key='group'>{{ group }} </span>
        </i><br>
        <i class="el-icon-info" v-if='params.eq'>
          停机设备: {{ params.eq }}
        </i><br>
        <i class="el-icon-info" v-if='params.search'>
          全局搜索: {{ params.search }}
        </i><br>
        <i class="el-icon-time">
          开始时间: {{ params.created_after | formatDate }}
        </i><br>
        <i class="el-icon-time">
          结束时间: {{ params.created_before | formatDate }}
        </i><br><br>
        <i class="el-icon-info">
          数量: {{ count }}
        </i>
      </el-col>
    </el-row>

    <el-row class='toolbox'>
      <!-- <el-button type="primary" plain @click="toggleSelection(['toggle'])">全部选择</el-button> -->
      <!-- <el-button type="primary" plain @click="toggleSelection()">取消选择</el-button> -->
      <!-- <QueryChart :selectId='selectId'></QueryChart> -->
      <QueryChart :params='params'></QueryChart>
      <Exporter title='导出 Excel 2007 ->' :params='params' format='xlsx'></Exporter>
      <Exporter title='导出 Excel 2003' :params='params' format='xls'></Exporter>
      <!-- <el-button type="success" @click='exporter("csv")'>导出 csv</el-button> -->
      <!-- <el-button type="success" @click='exporter("xlsx")'>导出 Excel 2007 -></el-button> -->
      <!-- <el-button type="success" @click='exporter("xls")'>导出 Excel 2003</el-button> -->
    </el-row>

    <el-table
      ref='orderTable'
      :data="orders"
      style="width: 100%"
      border
      header-row-class-name='table-header'
      @row-click='rowClick'
      :row-class-name="tableRowClassName"
      tooltip-effect="light"
    >
      <!-- <el-table-column type="selection" width="50"></el-table-column> -->
      <!-- <el-table-column type="index" width="50"></el-table-column> -->
      <el-table-column label="编号" min-width='150'>
        <template slot-scope="scope">
          <router-link
            :to="'/tft/order/detail/' + scope.row.id"
            class='id-href'
          >
            {{ scope.row.id }}
          </router-link>
        </template>
      </el-table-column>
      <el-table-column
        prop="status.desc"
        label="状态"
        min-width='180'
      ></el-table-column>
      <el-table-column prop="user.realname" label="开单人" min-width='80'>
        <template slot-scope="scope">
          {{ scope.row.user.realname }}
        </template>
      </el-table-column>
      <el-table-column
        prop="group.name"
        label="开单工程"
        min-width='100'
        column-key='group'
      ></el-table-column>
      <el-table-column prop="created" label="开单时间" :formatter='formatDate' min-width='150'></el-table-column>
      <el-table-column prop="found_step" label="发现站点" min-width='100' :show-overflow-tooltip='true'></el-table-column>
      <el-table-column prop="found_time" label="发现时间" :formatter='formatDate' min-width='150'></el-table-column>
      <el-table-column
        prop="charge_group.name"
        label="责任工程"
        min-width='100'
        column-key='charge_group'
      ></el-table-column>
      <el-table-column prop="eq" label="停机设备" min-width='100' :show-overflow-tooltip='true'></el-table-column>
      <el-table-column prop="kind" label="停机机种" min-width='100' :show-overflow-tooltip='true'></el-table-column>
      <el-table-column prop="step" label="停机站点" min-width='100' :show-overflow-tooltip='true'></el-table-column>
      <el-table-column
        label="绝对不良"
        min-width='100'
      >
        <template slot-scope="scope">
          <span v-if='scope.row.defect_type'>是</span>
          <span v-else-if='scope.row.defect_type === false'>否</span>
          <span v-else></span>
        </template>
      </el-table-column>
      <el-table-column prop="remarks[0].content" label="最新批注" min-width='100'></el-table-column>
    </el-table>

    <el-pagination
      background
      @current-change="handleCurrentChange"
      :current-page.sync="page"
      :page-size='pageSize'
      layout="total, prev, pager, next, jumper"
      :total="count"
    >
    </el-pagination>

    <BackTop></BackTop>

  </div>
</template>

<script>
import { formatDate } from '@/common/js/date.js'
import { getOrders, exporter } from '@/api/tft'
import BackTop from '@/common/components/BackTop'
import QueryChart from './QueryChart'
import Exporter from './Exporter'

export default {
  name: 'Query',
  data () {
    return {
      page: 1,
      pageSize: 15,
      count: null,
      orders: [],

      created: '',
      username: '',
      realname: '',
      status: ['1', '2', '4', '5', '6', '7', '8'],
      group: [],
      charge_group: [],
      eq: '',
      search: '',

      searchFlag: false,
      params: {},

      statusOptions: [
        {label: '（停机）待生产签核', value: '1'},
        {label: '（停机）待责任工程签核', value: '2'},
        {label: '（停机）拒签', value: '3'},
        {label: '（停机）完成', value: '4'},
        {label: '（复机）待QC签核', value: '5'},
        {label: '（复机）待生产签核', value: '6'},
        {label: '（复机）拒签', value: '7'},
        {label: '（部分复机）完成', value: '8'},
        {label: '（复机）完成', value: '9'}
      ],
      groupOptions: [
        {label: 'MFG', value: 'MFG'},
        {label: 'PVD', value: 'PVD'},
        {label: 'CVD', value: 'CVD'},
        {label: 'PHO', value: 'PHO'},
        {label: 'DRY', value: 'DRY'},
        {label: 'WET', value: 'WET'},
        {label: 'TEST', value: 'TEST'},
        {label: 'QC', value: 'QC'}
      ],

      pickerClock: {
        shortcuts: [{
          text: '最近一周',
          onClick (picker) {
            const end = new Date()
            const start = new Date()
            start.setTime(start.getTime() - 3600 * 1000 * 24 * 7)
            picker.$emit('pick', [start, end])
          }
        }, {
          text: '最近一个月',
          onClick (picker) {
            const end = new Date()
            const start = new Date()
            start.setTime(start.getTime() - 3600 * 1000 * 24 * 30)
            picker.$emit('pick', [start, end])
          }
        }, {
          text: '最近三个月',
          onClick (picker) {
            const end = new Date()
            const start = new Date()
            start.setTime(start.getTime() - 3600 * 1000 * 24 * 90)
            picker.$emit('pick', [start, end])
          }
        }]
      },
      selectId: []
    }
  },
  computed: {
    created_after () {
      return this.created ? this.created[0].toISOString() : undefined
    },
    created_before () {
      return this.created ? this.created[1].toISOString() : undefined
    }
  },
  methods: {
    getOrders () {
      getOrders({page: this.page, 'page-size': this.pageSize})
        .then((res) => {
          this.count = res.data.count
          this.orders = res.data.results
        })
        .catch((error) => {
          console.log(error)
        })
    },
    handleTips () {
      this.$notify({
        title: '检索说明',
        type: 'info',
        customClass: 'search-tips',
        dangerouslyUseHTMLString: true,
        message: `<li>忽略大小写</li>
                  <li>全局搜索：包括编号、工号、真名、修改人、发现站点、停机设备、停机机种、停机站点、停机原因、通知生产人员、通知生产人员、通知制程人员、异常描述、复机条件</li>
                  `,
        position: 'top-left',
        offset: 150
      })
    },
    handleSearch () {
      // 搜索后，从第一页开始显示
      this.page = 1
      let params = {page: this.page, 'page-size': this.pageSize}
      if (this.username.trim()) {
        params.username = this.username
      }
      if (this.realname.trim()) {
        params.realname = this.realname
      }
      if (this.status) {
        params.status = this.status
      }
      if (this.status.length) {
        params.status = this.status
      }
      if (this.group) {
        params.group = this.group
      }
      if (this.charge_group) {
        params.charge_group = this.charge_group
      }
      if (this.eq) {
        params.eq = this.eq
      }
      if (this.search.trim()) {
        params.search = this.search
      }
      if (this.created_after) {
        params.created_after = this.created_after
      }
      if (this.created_before) {
        params.created_before = this.created_before
      }
      this.params = params
      getOrders(this.params)
        .then((res) => {
          this.searchFlag = true
          this.orders = res.data.results
          this.count = res.data.count
        })
        .catch((err) => {
          console.log(err)
          this.orders = []
          this.count = null
        })
    },
    rowClick (row, event, column) {
      this.$router.push({path: `/tft/order/detail/${row.id}`})
    },
    formatDate (row, column, time, index) {
      let date = new Date(time)
      return formatDate(date, 'yyyy-MM-dd hh:mm:ss')
    },
    handleCurrentChange (val) {
      let params
      if (this.searchFlag) {
        params = this.params
        params.page = this.page
        params['page-size'] = this.pageSize
      } else {
        params = {page: this.page, 'page-size': this.pageSize}
      }
      getOrders(params)
        .then((res) => {
          this.count = res.data.count
          this.orders = res.data.results
        })
    },
    toggleSelection (rows) {
      if (rows) {
        // 选择传入的行
        // rows.forEach(row => {
        //   this.$refs.orderTable.toggleRowSelection(row)
        // })
        // 选择当前 orders 中所有
        // this.orders.forEach(row => {
        //   this.$refs.orderTable.toggleRowSelection(row)
        // })
        this.$refs.orderTable.toggleAllSelection()
      } else {
        this.$refs.orderTable.clearSelection()
      }
    },
    handleSelectionChange (val) {
      let ids = []
      val.forEach(row => {
        ids.push(row.id)
      })
      this.selectId = ids
    },
    exporter (format) {
      let params = this.params
      params['fmt'] = format
      exporter(params)
        .then((res) => {
          // let blob = new Blob([res.data], {type: 'text/plain;charset=utf-8'})
          // FileSaver.saveAs(blob, 'orders.csv')
          // 注意是否被浏览器拦截
          window.open(res.data.url, '_blank')
        })
        .catch((err) => {
          console.log(err)
        })
    },
    tableRowClassName ({row, rowIndex}) {
      if (row.status.code === '0') {
        return 'status0'
      } else if (row.status.code === '1') {
        return 'status1'
      } else if (row.status.code === '2') {
        return 'status2'
      } else if (row.status.code === '3') {
        return 'status3'
      } else if (row.status.code === '4') {
        return 'status4'
      } else if (row.status.code === '5') {
        return 'status5'
      } else if (row.status.code === '6') {
        return 'status6'
      } else if (row.status.code === '7') {
        return 'status7'
      } else if (row.status.code === '8') {
        return 'status8'
      } else if (row.status.code === '9') {
        return 'status9'
      }
      return 'status0'
    }
  },
  components: {
    BackTop,
    QueryChart,
    Exporter
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

<style lang='stylus'>
.search
  padding 10px
  border 1px dashed #000
  background #C7D4E1
  .search-label
    color #8C97A3
    font-size 15px
  .el-button
    margin-top 20px
.toolbox
  margin 20px
.table-header
  th
    font-size 1.1em
    background #CFD5DA
    text-align center
.id-href
  text-decoration none
  color #22558B
.el-table
  margin 30px 0
  td
    div
      max-height 2.2em
      overflow hidden
      white-space nowrap
      text-overflow ellipsis
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
  color #3A8FE7
.el-checkbox__inner
  border 1px dashed #8B8C8E
.el-pagination
  margin 20px 0
</style>
