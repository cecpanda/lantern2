<template>
  <div>
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
      <el-table-column prop="others" label="其他" width="100"></el-table-column>
    </el-table>
    <br><br><br><br>
    <v-chart :init-options='initOptions' :options='bar' :auto-resize='true'/>
    <br><br><br><br><br><br><br><br><br><br><br>
  </div>
</template>

<script>
import { getSummary } from '@/api/tft.js'
import ECharts from 'vue-echarts/components/ECharts.vue'
import 'echarts/lib/chart/bar'
import 'echarts/lib/chart/line'
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
      chartData: [],
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
        series: this.chartData
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
          this.chartData = res.data.chart
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

<style lang='stylus'>
.table-header
  th
    font-size 1.1em
    background #CFD5DA
.el-table__row:nth-child(2n)
  background-color #CFE0F1
.el-table__row:nth-child(2n+1)
  background-color #fff
</style>
