<template>
  <div class='table'>
    <h1>报表查询</h1>
    <el-row class='search'>
      <el-col :span='12'>
        <el-input v-model='search' placeholder="请输入内容" @keyup.enter.native='searching'>
          <el-select v-model="select" slot="prepend" placeholder="请选择">
            <el-option-group
              v-for="group in selectOptions"
              :key="group.label"
              :label="group.label"
            >
              <el-option
                v-for='option in group.options'
                :label="option.label"
                :value="option.value"
                :key='option.label'
              ></el-option>
            </el-option-group>
          </el-select>
          <el-button
            slot="append"
            icon="el-icon-search"
            @click='searching'
          ></el-button>
          <el-button
            slot="append"
            icon="el-icon-message"
            @click="openSearchMsg"
          ></el-button>
        </el-input>
      </el-col>
      <el-col :span='11' :offset='1'>
        <el-date-picker
          v-model="created"
          type="datetimerange"
          :picker-options="pickerClock"
          range-separator="至"
          start-placeholder="开始日期"
          end-placeholder="结束日期"
          align="right">
        </el-date-picker>
      </el-col>
    </el-row>
    <el-row
      v-if='searchFlag && Object.getOwnPropertyNames(this.searchText).length'
      class='searchConent'
    >
      <el-col :span='12' :offset='12'>
        <i class="el-icon-info"></i> {{ searchText.label }}: {{ searchText.text }} <br>
        <i class="el-icon-info"></i> 数量: {{ count }} <br>
        <i class="el-icon-time"></i> 开始时间: {{ this.showCreatedAfter | formatDate }} <br>
        <i class="el-icon-time"></i> 结束时间: {{ this.showCreatedBefore | formatDate }}
      </el-col>
    </el-row>
    <!-- <el-button @click='exportExcel'>exportExcel</el-button> -->
    <el-table
      ref='orderTable'
      :data="orders"
      style="width: 100%"
      border
      header-row-class-name='table-header'
      @row-click='rowClick'
      :row-class-name="tableRowClassName"
      @cell-mouse-enter='cellMouseEnter'
      @cell-mouse-leave='cellMouseLeave'
      @sort-change='sortChange'
      @filter-change='filterChange'
      tooltip-effect="light"
      @selection-change="handleSelectionChange"
    >
      <el-table-column type="selection" width="50"></el-table-column>
      <!-- <el-table-column type="index" width="50"></el-table-column> -->
      <el-table-column label="编号" min-width='120'>
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
        :filters='statusFilters'
        :filter-method='filterStatus'
      ></el-table-column>
      <el-table-column prop="user.realname" label="开单人" min-width='80'>
        <template slot-scope="scope">
          <!-- <UserPopover :username='scope.row.user.username' :key='scope.row.user.username'></UserPopover> -->
          {{ scope.row.user.realname }}
        </template>
      </el-table-column>
      <!-- <el-table-column prop="user.realname" label="真名" min-width='80'></el-table-column>
      <el-table-column label="开单人" min-width='100'>
        <template slot-scope="scope">
          <UserPopover :username='scope.row.user.username'></UserPopover>
        </template>
      </el-table-column> -->
      <el-table-column
        prop="group.name"
        label="开单工程"
        min-width='100'
        :filters='groupFilters'
        :filter-method='filterGroup'
        column-key='group'
      ></el-table-column>
      <!-- <el-table-column
        label="开单工程"
        min-width='100'
        :filters='groupFilters'
        :filter-method='filterGroup'
        column-key='group'
      >
        <template slot-scope="scope">
          {{ scope.row.group ? scope.row.group.name : ''}}
        </template>
      </el-table-column> -->
      <el-table-column prop="created" label="开单时间" :formatter='formatDate' min-width='150' sortable='custom'></el-table-column>
      <el-table-column prop="found_step" label="发现站点" min-width='100' :show-overflow-tooltip='true'></el-table-column>
      <el-table-column prop="found_time" label="发现时间" :formatter='formatDate' min-width='150'></el-table-column>
      <el-table-column
        prop="charge_group.name"
        label="责任工程"
        min-width='100'
        :filters='chargeGroupFilters'
        :filter-method='filterChargeGroup'
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

    <el-row class='toolbox'>
      <el-button type="primary" plain @click="toggleSelection(['toggle'])">全部选择</el-button>
      <el-button type="primary" plain @click="toggleSelection()">取消选择</el-button>
      <QueryChart :selectId='selectId'></QueryChart>
      <el-button type="success" @click='exporter("csv")'>导出 csv</el-button>
      <el-button type="success" @click='exporter("xlsx")'>导出 Excel 2007 -></el-button>
      <el-button type="success" @click='exporter("xls")'>导出 Excel 2003</el-button>
      <p class='button-tip'>
        注意，换页后之前的选择项失效！ <br>
        如果 csv 乱码：数据>自文本>导入>utf-8>下一步>逗号
      </p>
    </el-row>

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
import FileSaver from 'file-saver'
import XLSX from 'xlsx'
import { formatDate } from '@/common/js/date.js'
import { getOrders, exporter } from '@/api/tft'
import BackTop from '@/common/components/BackTop'
import UserPopover from '@/user/components/UserPopover'
import QueryChart from './QueryChart'

export default {
  name: 'Query',
  data () {
    return {
      page: 1,
      pageSize: 15,
      count: null,
      orders: [],
      // 留意 methods 中的 valueToLabel
      selectOptions: [{
        label: '模糊匹配',
        options: [
          {label: '所有', value: 'all'}
        ]
      }, {
        label: '精确匹配',
        options: [
          {label: '工号', value: 'username'},
          {label: '真名', value: 'realname'},
          {label: '开单工程', value: 'group'},
          {label: '责任工程', value: 'charge_group'}
        ]
      }],
      search: '',
      select: 'all',
      searchFlag: false,
      // 上次搜索的内容，直接使用 search 会出现动态效果,如 {label: '', value: '', text: ''}
      searchText: {},
      ordering: '',
      // {group: {'cvd', 'pvd'}, charge_group: {'cvd', 'pvd'}}
      filters: {},
      filterFlag: false,
      // search clock
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
      created: '',
      showCreatedAfter: '',
      showCreatedBefore: '',
      selectId: []
    }
  },
  computed: {
    created_after () {
      return this.created ? this.created[0].toISOString() : undefined
    },
    created_before () {
      return this.created ? this.created[1].toISOString() : undefined
    },
    params () {
      let params = {page: this.page, 'page-size': this.pageSize}
      if (this.select === 'username') {
        params.username = this.search
      } else if (this.select === 'realname') {
        params.realname = this.search
      } else if (this.select === 'group') {
        params.group = this.search
      } else if (this.select === 'charge_group') {
        params.charge_group = this.search
      } else {
        params.search = this.search
      }
      // 从 handleCurrentChange 中转移来的，与上面重复
      // if (this.searchFlag && Object.getOwnPropertyNames(this.searchText).length) {
      //   for (let key in this.searchText) {
      //     if (this.searchText.hasOwnProperty(key)) {
      //       params[key] = this.searchText[key]
      //     }
      //   }
      // }
      if (this.ordering) {
        params.ordering = this.ordering
      }
      // 因为 object 的属性不能重复，需重写同一属性对应多个值的过滤 API
      // if (this.filterFlag && Object.keys(this.filters).length) {
      //   for (let label in Object.keys(this.filters)) {
      //     if (this.filters[label].size) {
      //       this.filters[label].forEach((val) => {

      //       })
      //     }
      //   }
      // }
      if (this.created_after) {
        params.created_after = this.created_after
      }
      if (this.created_before) {
        params.created_before = this.created_before
      }
      return params
    },
    groupFilters () {
      // 在分页下得到不想要的结果
      // 只能过滤本页，切换页后，过滤状态保留
      let filters = []
      let values = new Set()
      this.orders.forEach((order) => {
        if (order.group) {
          values.add(order.group.name)
        }
      })
      values.forEach((value) => {
        filters.push({text: value, value: value})
      })
      return filters
    },
    chargeGroupFilters () {
      let filters = []
      let values = new Set()
      this.orders.forEach((order) => {
        values.add(order.charge_group.name)
      })
      values.forEach((value) => {
        filters.push({text: value, value: value})
      })
      return filters
    },
    statusFilters () {
      let filters = []
      let values = new Set()
      this.orders.forEach((order) => {
        values.add(order.status.desc)
      })
      values.forEach((value) => {
        filters.push({text: value, value: value})
      })
      return filters
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
    openSearchMsg () {
      this.$notify({
        title: '检索说明',
        type: 'info',
        customClass: 'search-msg',
        dangerouslyUseHTMLString: true,
        message: `<li>全部忽略大小写</li>
                  <li>所有：包括编号、工号、真名、修改人、发现站点、停机设备、停机机种、停机站点、停机原因、通知生产人员、通知生产人员、通知制程人员、异常描述、复机条件</li>
                  <li>表格头部：当前表格中的当前页的数据</li>
                  <li>其他: 搜索出的内容不再分页 - 为了表头过滤</li>
                  `,
        position: 'top-left',
        offset: 150
      })
    },
    formatDate (row, column, time, index) {
      let date = new Date(time)
      return formatDate(date, 'yyyy-MM-dd hh:mm:ss')
    },
    rowClick (row, event, column) {
      this.$router.push({path: `/tft/order/detail/${row.id}`})
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
    },
    rowStyle ({row, rowIndex}) {
    },
    cellMouseEnter (row, column, cell, event) {
    },
    cellMouseLeave (row, column, cell, event) {
    },
    // 根据 selectOption 中的 value 得到对应的 label，用来渲染
    valueToLabel (val) {
      // return 不能终止 forEach 循环,
      let label = ''
      this.selectOptions.forEach((selectOption) => {
        selectOption.options.forEach((option) => {
          if (option.value === val) {
            label = option.label
          }
        })
      })
      return label
    },
    searching () {
      // 搜索后，从第一页开始显示
      this.page = 1
      // let params = {page: this.page, 'page-size': this.pageSize}
      // if (this.select === 'username') {
      //   params.username = this.search
      // } else if (this.select === 'realname') {
      //   params.realname = this.search
      // } else if (this.select === 'group') {
      //   params.group = this.search
      // } else if (this.select === 'charge_group') {
      //   params.charge_group = this.search
      // } else {
      //   params.search = this.search
      // }
      getOrders(this.params)
        .then((res) => {
          // searchFlag
          this.searchFlag = true
          this.searchText = {} // 清空
          // searchText: {label: '', value: '', text: ''}
          let label = this.valueToLabel(this.select)
          this.searchText = {label: label, value: this.select, text: this.search}
          this.count = res.data.count
          // this.orders = res.data.results
          this.showCreatedAfter = this.created_after
          this.showCreatedBefore = this.created_before
          this.orders = []
          this.pageSize = res.data.count
          getOrders(this.params)
            .then((res) => {
              this.orders = res.data.results
            })
        })
        .catch((error) => {
          console.log(error)
        })
    },
    handleCurrentChange (val) {
      // let params = {page: this.page, 'page-size': this.pageSize}
      // if (this.searchFlag && Object.getOwnPropertyNames(this.searchText).length) {
      //   for (let key in this.searchText) {
      //     if (this.searchText.hasOwnProperty(key)) {
      //       params[key] = this.searchText[key]
      //     }
      //   }
      // }
      // if (this.ordering) {
      //   params.ordering = this.ordering
      // }
      getOrders(this.params)
        .then((res) => {
          this.count = res.data.count
          this.orders = res.data.results
        })
    },
    sortChange ({column, prop, order}) {
      // let params = {page: this.page, 'page-size': this.pageSize}
      // if (this.searchFlag && Object.getOwnPropertyNames(this.searchText).length) {
      //   for (let key in this.searchText) {
      //     if (this.searchText.hasOwnProperty(key)) {
      //       params[key] = this.searchText[key]
      //     }
      //   }
      // }
      if (order === 'descending') {
        prop = '-' + prop
      }
      this.ordering = prop
      // params.ordering = prop
      getOrders(this.params)
        .then((res) => {
          this.count = res.data.count
          this.orders = res.data.results
        })
        .catch((error) => {
          console.log(error)
        })
    },
    filterGroup (value, row, column) {
      // console.log('value:', value)
      // console.log('row:', row)
      // console.log('column:', column)
      return row.group.name === value
    },
    filterChargeGroup (value, row, column) {
      return row.charge_group.name === value
    },
    filterStatus (value, row, column) {
      return row.status.desc === value
    },
    filterChange (filters) {
      // console.log('change')
      // filters = {group: ['cvd', 'pvd']}
      this.filterFlag = true
      for (let key of (Object.keys(filters))) {
        this.filters[key] = new Set()
        filters[key].forEach((val) => {
          this.filters[key].add(val)
        })
      }
      // this.filters = {group: {'cvd', 'pvd'}, charge_group: {'cvd', 'pvd'}}
      // 同一属性对应多个值的过滤 API
      // console.log(this.filters)
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
      exporter({ids: this.selectId, format: format})
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
    exportExcel () {
      /* generate workbook object from table */
      var wb = XLSX.utils.table_to_book(document.querySelector('.el-table__body'))
      /* get binary string as output */
      var wbout = XLSX.write(wb, { bookType: 'xlsx', bookSST: true, type: 'array' })
      try {
        FileSaver.saveAs(new Blob([wbout], { type: 'application/octet-stream' }), 'sheetjs.xlsx')
      } catch (e) {
        if (typeof console !== 'undefined') {
          console.log(e, wbout)
        }
      }
      return wbout
    }
  },
  components: {
    BackTop,
    UserPopover,
    QueryChart
  },
  filters: {
    formatDate (time) {
      let date = new Date(time)
      return formatDate(date, 'yyyy-MM-dd hh:mm:ss')
    }
  },
  mounted () {
    this.getOrders(this.params)
  },
  destroyed () {
    this.orders = []
  }
}
</script>

<style lang='stylus'>
@import '~styles/varibles'
.search
  margin 20px 0
  .el-select
    width 100px
.search-msg
  background rgba(255, 255, 255, 0.7)
.searchConent
  margin-bottom 20px
.table-header
  th
    font-size 1.1em
    background #CFD5DA
    text-align center
.id-href
  text-decoration none
  color #22558B
.el-table
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
.el-checkbox__inner
  border 1px dashed #8B8C8E
.toolbox
  margin 10px 0 20px 0
.el-pagination
  margin 20px 0
.button-tip
  font-size 18px
  text-align right
</style>
