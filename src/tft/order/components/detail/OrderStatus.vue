<template>
  <div>
    <el-button
      type='success'
      size='small'
      @click='click'
    >
      {{ status }}
    </el-button>
    <el-dialog
      :title="id + ': ' + data.desc"
      :visible.sync="visible"
      width="60%"
    >
     <p>{{ data }}</p>
     <div id="status" ref='status'></div>
      <span slot="footer" class="dialog-footer">
        <el-button type="primary" @click="visible = false">关闭</el-button>
      </span>
    </el-dialog>
  </div>
</template>

<script>
import zrender from 'zrender'
import { getStatus } from '@/api/tft'

export default {
  name: 'OrderStatus',
  props: {
    id: {
      type: String,
      required: true
    },
    status: {}
  },
  data () {
    return {
      visible: false,
      data: {},
      current: '#2ECC19',
      ago: '#5EA1E7',
      defaultFill: '#fff',
      stroke: '#B4B7BB',
      agoStroke: '#5EA1E7',
      shadowColor: '#636B73',
      fontSize: 15,
      lineWidth: 8
    }
  },
  methods: {
    async click () {
      this.visible = true
      await this.getStatus()
      setTimeout(() => {
        this.draw()
      }, 600)
    },
    getStatus () {
      getStatus(this.id)
        .then((res) => {
          this.data = res.data
        })
        .catch((err) => {
          console.log(err)
        })
    },
    getFill (name) {
      if (name === this.data.current) {
        return this.current
      } else if (this.data.ago.indexOf(name) >= 0) {
        return this.ago
      }
      return this.defaultFill
    },
    getStroke (name) {
      if (this.data.ago.indexOf(name) >= 0) {
        return this.agoStroke
      }
      return this.stroke
    },
    mouseover (name) {
      name.animate('style', false)
        .when(100, {
          fontSize: this.fontSize * 1.3
        })
        .start()
      name.animate('scale', false)
        .when(100, [1.5, 1.5])
        .start()
    },
    mouseout (name) {
      name.animate('style', false)
        .when(100, {
          fontSize: this.fontSize
        })
        .start()
      name.animate('scale', false)
        .when(100, [1, 1])
        .start()
    },
    draw () {
      // let zr = zrender.init(document.getElementById('status'))
      let zr = zrender.init(this.$refs.status)
      let b1 = new zrender.Rect({
        draggable: true,
        shape: {
          x: 150,
          y: 25,
          width: 100,
          height: 25
        },
        style: {
          fill: this.getFill('b1'),
          stroke: this.getStroke('b1'),
          shadowBlur: 10,
          shadowColor: this.shadowColor,
          shadowOffsetX: '-5',
          shadowOffsetY: '5',
          text: '开始',
          fontSize: this.fontSize
        },
        origin: [200, 37]
      })
      b1.on('mouseover', () => {
        this.mouseover(b1)
      })
      b1.on('mouseout', () => {
        this.mouseout(b1)
      })
      zr.add(b1)

      let l1 = new zrender.Line({
        draggable: true,
        shape: {
          x1: 200,
          y1: 50,
          x2: 200,
          y2: 75
        },
        style: {
          stroke: this.getStroke('l1'),
          lineWidth: this.lineWidth
        }
      })
      zr.add(l1)

      let b2 = new zrender.Polygon({
        draggable: true,
        shape: {
          points: [[200, 75], [300, 87], [200, 100], [100, 87]],
          smooth: 0.3
        },
        style: {
          fill: this.getFill('b2'),
          stroke: this.getStroke('b2'),
          shadowBlur: 10,
          shadowColor: this.shadowColor,
          shadowOffsetX: '-5',
          shadowOffsetY: '5',
          text: '开单工程是否为 QC',
          fontSize: this.fontSize
        },
        origin: [200, 87]
      })
      b2.on('mouseover', () => {
        this.mouseover(b2)
      })
      b2.on('mouseout', () => {
        this.mouseout(b2)
      })
      zr.add(b2)

      let l2 = new zrender.Line({
        draggable: true,
        shape: {
          x1: 200,
          y1: 100,
          x2: 100,
          y2: 150
        },
        style: {
          stroke: this.getStroke('l2'),
          text: 'N',
          textPosition: 'inside',
          lineWidth: this.lineWidth
        }
      })
      zr.add(l2)

      let b3 = new zrender.Rect({
        draggable: true,
        shape: {
          x: 50,
          y: 150,
          width: 100,
          height: 25
        },
        style: {
          fill: this.getFill('b3'),
          stroke: this.getStroke('b3'),
          shadowBlur: 10,
          shadowColor: this.shadowColor,
          shadowOffsetX: '-5',
          shadowOffsetY: '5',
          text: '生产签核',
          fontSize: this.fontSize
        },
        origin: [100, 162]
      })
      b3.on('mouseover', () => {
        this.mouseover(b3)
      })
      b3.on('mouseout', () => {
        this.mouseout(b3)
      })
      zr.add(b3)

      let l3 = new zrender.Line({
        draggable: true,
        shape: {
          x1: 100,
          y1: 175,
          x2: 100,
          y2: 362
        },
        style: {
          stroke: this.getStroke('l3'),
          lineWidth: this.lineWidth
        }
      })
      zr.add(l3)

      let l4 = new zrender.Line({
        draggable: true,
        shape: {
          x1: 100,
          y1: 362,
          x2: 150,
          y2: 362
        },
        style: {
          stroke: this.getStroke('l4'),
          lineWidth: this.lineWidth
        }
      })
      zr.add(l4)

      let l5 = new zrender.Line({
        draggable: true,
        shape: {
          x1: 200,
          y1: 100,
          x2: 300,
          y2: 150
        },
        style: {
          stroke: this.getStroke('l5'),
          text: 'Y',
          textPosition: 'inside',
          lineWidth: this.lineWidth
        }
      })
      zr.add(l5)

      let b4 = new zrender.Polygon({
        draggable: true,
        shape: {
          points: [[300, 150], [350, 162], [300, 175], [250, 162]],
          smooth: 0.3
        },
        style: {
          fill: this.getFill('b4'),
          stroke: this.getStroke('b4'),
          shadowBlur: 10,
          shadowColor: this.shadowColor,
          shadowOffsetX: '-5',
          shadowOffsetY: '5',
          text: '绝对不良',
          fontSize: this.fontSize
        },
        origin: [300, 162]
      })
      b4.on('mouseover', () => {
        this.mouseover(b4)
      })
      b4.on('mouseout', () => {
        this.mouseout(b4)
      })
      zr.add(b4)

      let l6 = new zrender.Line({
        draggable: true,
        shape: {
          x1: 300,
          y1: 175,
          x2: 200,
          y2: 225
        },
        style: {
          stroke: this.getStroke('l6'),
          text: 'N',
          textPosition: 'inside',
          lineWidth: this.lineWidth
        }
      })
      zr.add(l6)

      let b5 = new zrender.Rect({
        draggable: true,
        shape: {
          x: 150,
          y: 225,
          width: 100,
          height: 25
        },
        style: {
          fill: this.getFill('b5'),
          stroke: this.getStroke('b5'),
          shadowBlur: 10,
          shadowColor: this.shadowColor,
          shadowOffsetX: '-5',
          shadowOffsetY: '5',
          text: '责任工程签核',
          fontSize: this.fontSize
        },
        origin: [200, 237]
      })
      b5.on('mouseover', () => {
        this.mouseover(b5)
      })
      b5.on('mouseout', () => {
        this.mouseout(b5)
      })
      zr.add(b5)

      let l7 = new zrender.Line({
        draggable: true,
        shape: {
          x1: 200,
          y1: 250,
          x2: 200,
          y2: 275
        },
        style: {
          stroke: this.getStroke('l7'),
          text: 'Y',
          textPosition: 'inside',
          lineWidth: this.lineWidth
        }
      })
      zr.add(l7)

      let b6 = new zrender.Rect({
        draggable: true,
        shape: {
          x: 150,
          y: 275,
          width: 100,
          height: 25
        },
        style: {
          fill: this.getFill('b6'),
          stroke: this.getStroke('b6'),
          shadowBlur: 10,
          shadowColor: this.shadowColor,
          shadowOffsetX: '-5',
          shadowOffsetY: '5',
          text: '生产签核',
          fontSize: this.fontSize
        },
        origin: [200, 287]
      })
      b6.on('mouseover', () => {
        this.mouseover(b6)
      })
      b6.on('mouseout', () => {
        this.mouseout(b6)
      })
      zr.add(b6)

      let l8 = new zrender.Line({
        draggable: true,
        shape: {
          x1: 250,
          y1: 237,
          x2: 350,
          y2: 362
        },
        style: {
          stroke: this.getStroke('l8'),
          text: 'N',
          textPosition: 'inside',
          lineWidth: this.lineWidth
        }
      })
      zr.add(l8)

      let l9 = new zrender.Line({
        draggable: true,
        shape: {
          x1: 200,
          y1: 300,
          x2: 200,
          y2: 350
        },
        style: {
          stroke: this.getStroke('l9'),
          lineWidth: this.lineWidth
        }
      })
      zr.add(l9)

      let l10 = new zrender.Line({
        draggable: true,
        shape: {
          x1: 300,
          y1: 175,
          x2: 400,
          y2: 225
        },
        style: {
          stroke: this.getStroke('l10'),
          text: 'Y',
          lineWidth: this.lineWidth
        }
      })
      zr.add(l10)

      let b7 = new zrender.Rect({
        draggable: true,
        shape: {
          x: 350,
          y: 225,
          width: 100,
          height: 25
        },
        style: {
          fill: this.getFill('b7'),
          stroke: this.getStroke('b7'),
          shadowBlur: 10,
          shadowColor: this.shadowColor,
          shadowOffsetX: '-5',
          shadowOffsetY: '5',
          text: '生产签核',
          fontSize: this.fontSize
        },
        origin: [400, 237]
      })
      b7.on('mouseover', () => {
        this.mouseover(b7)
      })
      b7.on('mouseout', () => {
        this.mouseout(b7)
      })
      zr.add(b7)

      let l11 = new zrender.Line({
        draggable: true,
        shape: {
          x1: 400,
          y1: 250,
          x2: 400,
          y2: 275
        },
        style: {
          stroke: this.getStroke('l11'),
          lineWidth: this.lineWidth
        }
      })
      zr.add(l11)

      let b8 = new zrender.Rect({
        draggable: true,
        shape: {
          x: 350,
          y: 275,
          width: 100,
          height: 25
        },
        style: {
          fill: this.getFill('b8'),
          stroke: this.getStroke('b8'),
          shadowBlur: 10,
          shadowColor: this.shadowColor,
          shadowOffsetX: '-5',
          shadowOffsetY: '5',
          text: '责任工程签核',
          fontSize: this.fontSize
        },
        origin: [400, 282]
      })
      b8.on('mouseover', () => {
        this.mouseover(b8)
      })
      b8.on('mouseout', () => {
        this.mouseout(b8)
      })
      zr.add(b8)

      let l12 = new zrender.Line({
        draggable: true,
        shape: {
          x1: 400,
          y1: 300,
          x2: 400,
          y2: 350
        },
        style: {
          stroke: this.getStroke('l12'),
          text: 'N',
          lineWidth: this.lineWidth
        }
      })
      zr.add(l12)

      let l13 = new zrender.Line({
        draggable: true,
        shape: {
          x1: 400,
          y1: 300,
          x2: 250,
          y2: 362
        },
        style: {
          stroke: this.getStroke('l13'),
          text: 'Y',
          textOffset: [20, -10],
          lineWidth: this.lineWidth
        }
      })
      zr.add(l13)

      let b9 = new zrender.Rect({
        draggable: true,
        shape: {
          x: 350,
          y: 350,
          width: 100,
          height: 25
        },
        style: {
          fill: this.getFill('b9'),
          stroke: this.getStroke('b9'),
          shadowBlur: 10,
          shadowColor: this.shadowColor,
          shadowOffsetX: '-5',
          shadowOffsetY: '5',
          text: '拒签',
          fontSize: this.fontSize
        },
        origin: [400, 362]
      })
      b9.on('mouseover', () => {
        this.mouseover(b9)
      })
      b9.on('mouseout', () => {
        this.mouseout(b9)
      })
      zr.add(b9)

      let b10 = new zrender.Rect({
        draggable: true,
        shape: {
          x: 150,
          y: 350,
          width: 100,
          height: 25
        },
        style: {
          fill: this.getFill('b10'),
          stroke: this.getStroke('b10'),
          shadowBlur: 10,
          shadowColor: this.shadowColor,
          shadowOffsetX: '-5',
          shadowOffsetY: '5',
          text: '责任工程签核',
          fontSize: this.fontSize
        },
        origin: [200, 362]
      })
      b10.on('mouseover', () => {
        this.mouseover(b10)
      })
      b10.on('mouseout', () => {
        this.mouseout(b10)
      })
      zr.add(b10)
    }
  }
}
</script>

<style lang='stylus' scoped>
#status
  border 1px solid #000
  width 500px
  height 800px
</style>
