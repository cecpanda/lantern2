<template>
  <div class="exporter">
    <el-button type="success" @click="click">{{ title }}</el-button>
    <el-dialog
      :title="title"
      :visible.sync="visible"
      width='80%'
    >
      <h3>请等待不超过 {{ countDown }} 秒, 请勿关闭</h3>
      <div
        v-loading='loading'
        element-loading-text="拼命下载中"
        element-loading-spinner="el-icon-loading"
      >
        {{ err }}
      </div>
    </el-dialog>
  </div>
</template>

<script>
import { exporter } from '@/api/tft'

export default {
  name: 'Exporter',
  props: {
    title: {
      required: true,
      type: String
    },
    format: {
      required: true,
      type: String
    },
    params: {
      required: true,
      type: Object
    }
  },
  data () {
    return {
      visible: false,
      countDown: 100,
      loading: true,
      err: ''
    }
  },
  methods: {
    click () {
      this.visible = true
      let params = this.params
      params['fmt'] = this.format

      exporter(params)
        .then((res) => {
          // 注意是否被浏览器拦截
          window.open(res.data.url, '_blank')
          this.close()
        })
        .catch((err) => {
          this.err = err
          this.close()
        })
      this.timer = setInterval(() => {
        this.countDown -= 1
      }, 1000)
    },
    close () {
      this.visible = false
      this.countDown = 100
      if (this.timer) {
        clearInterval(this.timer)
      }
    }
  }
}
</script>

<style lang='stylus' scoped>
.exporter
  display inline
  h3
    font-size 35px
</style>
