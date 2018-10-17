<template>
  <div>
    <el-row>
      <el-col :xs='24' :sm='24' :md='12' :lg='12' :xl='12'>
        <el-table
          :data='tableData'
          border
          header-row-class-name='table-header'
        >
          <el-table-column prop="group" label="责任工程" width="100"></el-table-column>
          <el-table-column prop="sum" label="停机单数" width="100"></el-table-column>
          <el-table-column prop="audits" label="停机审核中" width="100"></el-table-column>
          <el-table-column prop="rejects" label="停机拒签" width="100"></el-table-column>
          <el-table-column prop="closed" label="停机完成" width="100"></el-table-column>
          <el-table-column prop="finished" label="已复机" width="100"></el-table-column>
        </el-table>
      </el-col>
      <el-col :xs='18' :sm='18' :md='8' :lg='8' :xl='8' :offset='4' class='notice'>
        <h3>公告</h3>
      </el-col>
    </el-row>
    <el-row class='chart'>
      <el-col :xs='24' :sm='24' :md='11' :lg='11' :xl='11'>
        <v-chart :options='pie' :auto-resize='true'/>
      </el-col>
      <el-col :xs='24' :sm='24' :md='13' :lg='13' :xl='13'>
        <v-chart :init-options='initOptions' :options='bar' :auto-resize='true'/>
        <br><br><br><br><br><br><br><br><br><br><br><br>
      </el-col>
    </el-row>
  </div>
</template>

<script>
import { getSummary } from '@/api/tft.js'
import ECharts from 'vue-echarts/components/ECharts.vue'
import 'echarts/lib/chart/bar'
import 'echarts/lib/chart/line'
import 'echarts/lib/chart/pie'
import 'echarts/lib/component/title'
import 'echarts/lib/component/tooltip'
import 'echarts/lib/component/toolbox'
import 'echarts/lib/component/legend'

export default {
  name: 'Summary',
  data () {
    return {
      groups: [],
      tableData: [],
      barData: [],
      pieData: [],
      initOptions: {
        width: 800,
        height: 600
      }
    }
  },
  computed: {
    bar () {
      return {
        title: {
          text: '责任工程停机单状况'
        },
        tooltip: {},
        toolbox: {
          feature: {
            saveAsImage: {},
            dataView: {},
            dataZoom: {},
            restore: {}
          }
        },
        legend: {
          top: 30,
          data: ['停机单数', '停机审核中', '停机拒签', '停机完成', '已复机', '其他']
        },
        xAxis: {
          data: this.groups
        },
        yAxis: {},
        series: this.barData
      }
    },
    pie () {
      return {
        title: {
          text: '责任工程停机单状况'
        },
        tooltip: {
          trigger: 'item',
          formatter: '{a}: {b} <br> 数量: {c} <br> 比例: {d}%'
        },
        toolbox: {
          feature: {
            saveAsImage: {},
            dataView: {},
            restore: {}
          }
        },
        legend: {
          orient: 'vertical',
          left: 'left',
          top: '30',
          data: this.groups
        },
        series: [{
          name: '责任工程',
          type: 'pie',
          data: this.pieData
        }]
      }
    }
  },
  methods: {
    getSummary () {
      // getSummary('group')
      //   .then((res) => {
      //     this.groups = res.data.groups
      //     this.groupData = res.data.data
      //   })
      //   .catch((err) => {
      //     console.log(err)
      //   })
      getSummary('charge_group')
        .then((res) => {
          this.groups = res.data.groups
          this.tableData = res.data.table
          this.barData = res.data.bar
          this.pieData = res.data.pie
        })
        .catch((err) => {
          console.log(err)
        })
    }
  },
  components: {
    vChart: ECharts
  },
  mounted () {
    this.getSummary()
  }
}
</script>

<style lang='stylus' scoped>
.table-header
  th
    font-size 1.1em
    background #CFD5DA
.el-table__row:nth-child(2n)
  background-color #CFE0F1
.el-table__row:nth-child(2n+1)
  background-color #fff
.notice
  border 1px dashed #000
  min-height 200px
  box-shadow -10px 10px 10px #ccc
.chart
  margin-top 50px
</style>
