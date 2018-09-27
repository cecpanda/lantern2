<template>
  <div class='query-chart'>
    <el-button type="success" @click="click">图表统计</el-button>
    <el-dialog title="责任工程图表统计" :visible.sync="visible" width='80%'>
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
      <v-chart :options='pie' :auto-resize='true'/>
    </el-dialog>
  </div>
</template>

<script>
import ECharts from 'vue-echarts/components/ECharts.vue'
import 'echarts/lib/chart/bar'
import 'echarts/lib/chart/line'
import 'echarts/lib/chart/pie'
import 'echarts/lib/component/title'
import 'echarts/lib/component/tooltip'
import 'echarts/lib/component/toolbox'
import 'echarts/lib/component/legend'
import { drawChart } from '@/api/tft'

export default {
  props: {
    ids: {
      required: true,
      type: Array
    }
  },
  data () {
    return {
      visible: false,
      groups: [],
      tableData: [],
      barData: [],
      pieData: [],
      initOptions: {
        width: 600,
        height: 400
      }
    }
  },
  computed: {
    bar () {
      return {
        title: {
          text: '各状态停机单统计'
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
          text: '各工程占比统计'
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
      drawChart(this.ids)
        .then((res) => {
          this.groups = res.data.groups
          this.tableData = res.data.table
          this.barData = res.data.bar
          this.pieData = res.data.pie
        })
        .catch((err) => {
          console.log(err)
        })
    },
    click () {
      this.visible = true
      this.getSummary()
    }
  },
  components: {
    vChart: ECharts
  }
  // mounted () {
  //   this.getSummary()
  // }
}
</script>

<style lang='stylus' scoped>
.query-chart
  display inline
.table-header
  th
    font-size 1.1em
    background #CFD5DA
.el-table__row:nth-child(2n)
  background-color #CFE0F1
.el-table__row:nth-child(2n+1)
  background-color #fff
</style>
