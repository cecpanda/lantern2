<template>
  <div>
    <canvas ref='canvas' id="canvas" width='500' height='500'></canvas>
  </div>
</template>

<script>
export default {
  name: 'Clock',
  data () {
    return {
      date: new Date()
    }
  },
  computed: {
  },
  methods: {
    clock () {
      // let ctx = document.getElementById('canvas').getContext('2d')
      let ctx = this.$refs['canvas'].getContext('2d')
      let sec = this.date.getSeconds()
      let min = this.date.getMinutes()
      let hr = this.date.getHours()
      hr = hr > 12 ? hr - 12 : hr

      // 绘制表盘底色
      ctx.save()
      ctx.clearRect(0, 0, 400, 400)
      ctx.translate(200, 200)
      ctx.rotate(-Math.PI / 2) // 将坐标轴逆时针旋转90度，x轴正方向对准12点方向
      var lingrad = ctx.createLinearGradient(150, 0, -150, 0)
      lingrad.addColorStop(0, '#242f37')
      lingrad.addColorStop(1, '#48585c')
      ctx.fillStyle = lingrad
      ctx.beginPath()
      ctx.arc(0, 0, 150, 0, Math.PI * 2, true)
      ctx.fill()

      ctx.save()
      for (var i = 0; i < 12; i++) {
        ctx.beginPath()
        ctx.strokeStyle = '#fff'
        ctx.lineWidth = 3
        ctx.rotate(Math.PI / 6)
        ctx.moveTo(140, 0)
        ctx.lineTo(120, 0)
        ctx.stroke()
      }
      ctx.restore()

      ctx.save()
      ctx.beginPath()
      for (i = 0; i < 60; i++) {
        if (i % 5 !== 0) {
          ctx.beginPath()
          ctx.strokeStyle = '#536b7a'
          ctx.lineWidth = 2
          ctx.moveTo(140, 0)
          ctx.lineTo(130, 0)
          ctx.stroke()
        }
        ctx.rotate(Math.PI / 30)
      }
      ctx.restore()
      // 时间文字
      ctx.save()
      ctx.rotate(Math.PI / 2)
      ctx.beginPath()
      ctx.fillStyle = '#58717e'
      ctx.font = '32px Microsoft yahei'
      ctx.textAlign = 'center'
      ctx.textBaseline = 'middle'
      ctx.fillText('3', 100, 0)
      ctx.fillText('6', 0, 100)
      ctx.fillText('9', -100, 0)
      ctx.fillText('12', 0, -100)
      ctx.restore()
      // 时针
      ctx.save()
      ctx.rotate(hr * (Math.PI / 6) + min * (Math.PI / 360) + sec * (Math.PI / 21600))
      ctx.lineWidth = 10
      ctx.strokeStyle = '#fff'
      ctx.beginPath()
      ctx.moveTo(0, 0)
      ctx.lineTo(110, 0)
      ctx.stroke()
      ctx.fillStyle = '#34434c'
      ctx.arc(102, 0, 3, 0, Math.PI * 2, true)
      ctx.fill()
      ctx.restore()
      // 分针
      ctx.save()
      ctx.rotate(min * (Math.PI / 30) + sec * (Math.PI / 1800))
      ctx.lineWidth = 6
      ctx.strokeStyle = '#fff'
      ctx.beginPath()
      ctx.moveTo(0, 0)
      ctx.lineTo(135, 0)
      ctx.stroke()

      ctx.lineWidth = 3
      ctx.strokeStyle = '#34434c'
      ctx.beginPath()
      ctx.moveTo(130, 0)
      ctx.lineTo(115, 0)
      ctx.stroke()
      ctx.restore()
      // 秒针
      ctx.save()
      ctx.rotate(sec * (Math.PI / 30))
      ctx.beginPath()
      ctx.lineWidth = 4
      ctx.strokeStyle = '#fff'
      ctx.moveTo(0, 0)
      ctx.lineTo(141, 0)
      ctx.stroke()

      ctx.beginPath()
      ctx.lineWidth = 12
      ctx.strokeStyle = '#fff'
      ctx.moveTo(0, 0)
      ctx.lineTo(-38, 0)
      ctx.stroke()

      ctx.beginPath()
      ctx.fillStyle = '#fff'
      ctx.arc(0, 0, 15, 0, Math.PI * 2, true)
      ctx.fill()

      ctx.beginPath()
      ctx.strokeStyle = '#cdd2d5'
      ctx.lineWidth = 1
      ctx.arc(0, 0, 8, 0, Math.PI * 2, true)
      ctx.stroke()
      ctx.restore()
      // 边框
      ctx.beginPath()
      ctx.lineWidth = 7
      var lingrad2 = ctx.createLinearGradient(150, 0, -150, 0)
      lingrad2.addColorStop(0, '#adb9c5')
      lingrad2.addColorStop(1, '#e9eced')
      ctx.strokeStyle = lingrad2
      ctx.arc(0, 0, 152, 0, Math.PI * 2, true)
      ctx.stroke()
      ctx.restore()
    }
  },
  watch: {
    date () {
      this.clock()
    }
  },
  mounted () {
    this.clock()
    var _this = this // 声明一个变量指向vue实例this,保证作用域一致
    this.timer = setInterval(() => {
      _this.date = new Date() // 修改数据date
    }, 1000)
  },
  beforeDestroy () {
    if (this.timer) {
      clearInterval(this.timer) // 在vue实例销毁钱，清除我们的定时器
    }
  }
}
</script>

<style lang='stylus' scoped>
//   border 1px solid #eee
//   position: relative
//   left 300px
//   top 300px
//   transform: translate(-50%, -50%)
</style>
