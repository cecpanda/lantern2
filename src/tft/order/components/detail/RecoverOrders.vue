<template>
  <div class="recover-orders" v-if='recoverorders.length'>
    <el-row>
      <el-col :xs="24" :sm="24" :md="24" :lg="24" :xl="24">
        <h1 class=title>设备品质异常复机单</h1>
      </el-col>
    </el-row>

    <!-- <div v-for='(recoverorder, index) in recoverorders' :key='index' class='recover-order'>
      <RecoverOrder
        :recoverorder='recoverorder'
        :index='index'
        @change='handleChange'
        :status='status'
      ></RecoverOrder>
    </div> -->

    <el-carousel class='recover-order' height='730px'>
      <el-carousel-item v-for='(recoverorder, index) in recoverorders' :key='index'>
        <RecoverOrder
          :recoverorder='recoverorder'
          @change='handleChange'
          :status='status'
        ></RecoverOrder>
      </el-carousel-item>
    </el-carousel>

  </div>
</template>

<script>
import RecoverOrder from './RecoverOrder'

export default {
  name: 'RecoverOrders',
  props: {
    status: {
      required: true
    },
    recoverorders: {
      type: Array,
      required: true
    }
  },
  methods: {
    handleChange () {
      this.$emit('change')
    }
  },
  components: {
    RecoverOrder
  }
}
</script>

<style lang='stylus'>
.recover-orders
  border 1px solid #0E67CC
  border-radius 20px
  box-shadow -8px 8px 5px #888888
  margin-bottom 20px
  .recover-order
    border 2px double #4FA110
    .recover-order-title
      text-align center
      color #2E77C2
      // text-shadow 2px 2px 5px #2E77C2
.title
  text-align center
.el-form-item
  word-break break-all
.label
  font-size 1rem
  // border 1px solid red
  text-align right
  min-height 1rem
  line-height 1rem
  margin-bottom 20px
  word-break break-all
  word-wrap break-word
  background-color #C1C8CF
.content
  // border 1px solid red
  color #1B7FE5
  font-size .8rem
  text-align left
  min-height 1rem
  line-height 1rem
  margin-bottom 20px
  padding-left 0.5rem
  word-break break-all
  word-wrap break-word
  a
    text-decoration none
    color #22558B
  .remark-user
    color #000
.el-carousel
  z-index 0
.el-carousel__item:nth-child(2n)
  background-color #CFE0F1
.el-carousel__item:nth-child(2n+1)
  background-color #D7EED9
// 想改这个样式，记得去 scoped
.el-carousel__arrow
  background-color #8C9095
.el-carousel__button
  height 7px
  background-color #8C9095
.el-carousel__indicator.is-active
  button
    background-color #000
</style>
