<template>
  <div>
    <div class="shopcart">
      <div class="content">
        <div class="content-left">
          <div class="logo-wrapper" @click="changeFold">
            <div class="logo" :class="{'highLight' : totalCount > 0}">
              <i class="icon-shopping_cart" :class="{'highLight' : totalCount > 0}"></i>
            </div>
            <div v-show="totalCount > 0" class="num">{{totalCount}}</div>
          </div>
          <div class="price">
            <span class="text" :class="{'highLight' : totalCount > 0}">￥{{totalPrice}}</span>
          </div>
          <div class="desc">
            <span class="text">另需配送费￥{{deliveryPrice}}元</span>
          </div>
        </div>
        <div class="content-right" :class="{'highLight' : totalPrice >= minPrice}" @click="pay">
          <div class="pay" :class="{'highLight' : totalPrice >= minPrice}">
            {{payDesc}}
          </div>
        </div>
      </div>
      <div class="ball-container" v-for="ball in balls" :key="ball.index">
       <transition name="drop" @before-enter="beforeDrop" @enter="dropping" @after-enter="afterDrop">
        <div class="ball" v-show="ball.show"></div>
       </transition>
      </div>
      <transition name="fold">
        <div v-show="Fold" class="shopcart-list">
          <div class="list-header">
            <h1 class="title">购物车</h1>
            <span class="empty" @click="clearList">清空</span>
          </div>
          <div class="list-content" ref="listWrapper">
            <ul class="list">
              <li v-for="food in selectFoods" class="food" :key="food.index">
                <span class="name">
                  {{food.name}}
                </span>
                <div class="price">
                  <span>￥{{food.price * food.count}}</span>
                </div>
                <div class="cartcontrol-wrapper">
                  <cartcontrol :food='food' @decrease="changeShow"></cartcontrol>
                </div>
              </li>
            </ul>
          </div>
        </div>
      </transition>
    </div>
    <transition name="blur">
      <div v-show="Fold" class="list-mask" @click="changeFold()"></div>
    </transition>
  </div>
</template>

<script>
import cartcontrol from '../cartcontrol/cartcontrol'
import BScroll from 'better-scroll'

export default {
  data () {
    return {
      Fold: false,
      balls: [
        {
          show: false
        },
        {
          show: false
        },
        {
          show: false
        },
        {
          show: false
        },
        {
          show: false
        }
      ],
      dropBalls: []
    }
  },
  components: {
    cartcontrol
  },
  methods: {
    changeShow () {
      if (this.totalCount === 0) {
        this.Fold = !this.Fold
      }
    },
    _initScroll () {
      this.listScroll = new BScroll(this.$refs.listWrapper, {
        click: true
      })
    },
    changeFold () {
      if (this.showFold) {
        this.Fold = !this.Fold
        if (!this.listScroll) {
          this.$nextTick(() => {
            this._initScroll()
          })
        } else {
          this.listScroll.refresh()
        }
      }
    },
    clearList () {
      this.selectFoods.forEach((food) => {
        food.count = 0
      })
      this.Fold = false
    },
    pay () {
      if (this.totalPrice < this.minPrice) {
        return
      }
      window.alert(`需要支付${this.totalPrice}元`)
    },
    _drop (target) {
      for (let i = 0; i < this.balls.length; i++) {
        if (!this.balls[i].show) {
          this.balls[i].show = true
          this.balls[i].target = target
          this.dropBalls.push(this.balls[i])
          return
        }
      }
    },
    beforeDrop (target) {
      let count = this.balls.length
      while (count--) {
        let ball = this.balls[count]
        if (ball.show) {
          let rect = ball.target.getBoundingClientRect()
          let x = rect.left - 32
          let y = -(window.innerHeight - rect.top - 22)
          console.log(x)
          console.log(y)
          target.style.display = ''
          target.style.webkitTransform = `translate3D(${x}px,${y}px,0)`
          target.style.transform = `translate3D(${x}px,${y}px,0)`
        }
      }
    },
    dropping (target) {
      /* eslint-disable no-unused-vars */
      let rf = target.offsetHeight
      this.$nextTick(() => {
        target.style.webkitTransform = 'translate3D(0,0,0)'
        target.style.transform = 'translate3D(0,0,0)'
      })
    },
    afterDrop (target) {
      let ball = this.dropBalls.shift()
      if (ball) {
        ball.show = false
        ball.display = 'none'
      }
    }
  },
  props: {
    selectFoods: {
      type: Array,
      default () {
        return []
      }
    },
    deliveryPrice: {
      type: Number
    },
    minPrice: {
      type: Number
    }
  },
  computed: {
    totalPrice () {
      let total = 0
      this.selectFoods.forEach((food) => {
        total += food.price * food.count
      })
      return total
    },
    totalCount () {
      let count = 0
      this.selectFoods.forEach((food) => {
        count += food.count
      })
      return count
    },
    payDesc () {
      if (this.totalPrice === 0) {
        return `￥${this.minPrice}起送`
      } else if (this.totalPrice < this.minPrice) {
        return `还差￥${this.minPrice - this.totalPrice}起送`
      } else {
        return '去结算'
      }
    },
    showFold () {
      if (this.totalCount > 0) {
        return true
      }
      return false
    }
  }
}
</script>

<style lang="stylus">
@import '../../common/stylus/mixin.styl'

.shopcart
  position fixed
  left 0
  bottom 0
  z-index 50
  height 48px
  width 100%
  .content
    display flex
    background-color #141d27
    .content-left
      flex 1
      .logo-wrapper
        display inline-block
        position relative
        top -10px
        width 56px
        height 56px
        padding 6px
        margin 0 12px
        border-radius 50%
        vertical-align top
        box-sizing border-box
        background-color #141d27
        .logo
          width 44px
          height 44px
          background-color rgba(255,255,255,0.4)
          border-radius 50%
          text-align center
          &.highLight
            background-color rgb(0,160,220)
          .icon-shopping_cart
            font-size 24px
            color rgba(255,255,255,0.4)
            line-height 44px
            &.highLight
              color #fff
        .num
          position absolute
          right 0px
          top 0px
          width 24px
          height 16px
          line-height 16px
          border-radius 16px
          text-align center
          font-size 9px
          font-weight 700
          color #fff
          background-color rgb(240,20,20)
          box-shadow 0px 4px 8px 0px rgba(0,0,0,0.4)
      .price
        display inline-block
        margin 12px 0
        padding-right 12px
        border-right 1px solid rgba(255,255,255,0.1)
        .text
          font-size 16px
          color rgba(255,255,255,0.4)
          font-weight 700
          line-height 24px
          &.highLight
            color #fff
      .desc
        display inline-block
        margin 12px 0 12px 12px
        .text
          font-size 12px
          color rgba(255,255,255,0.4)
          font-weight 200
          line-height 28px
    .content-right
      flex 0 0 105px
      width 105px
      height 48px
      font-size 12px
      font-weight 700
      line-height 24px
      text-align center
      &.highLight
        background-color #00b43c
      .pay
        padding 12px 8px
        background-color rgba(255,255,255,0.4)
        color rgba(255,255,255,0.4)
        &.highLight
          color #fff
          padding 12px 8px
          color rgb(255,255,255)
  .ball-container
    .ball
      position: fixed
      left: 32px
      bottom: 22px
      z-index: 200
      transition: all 0.4s cubic-bezier(0.49, -0.29, 0.75, 0.41)
      width: 16px
      height: 16px
      border-radius: 50%
      background: rgb(0, 160, 220)
  .shopcart-list
    position absolute
    top 0
    left 0
    z-index -1
    max-height 257px
    width 100%
    font-size 14px
    color rgb(7,17,27)
    transition all 0.5s
    transform translate3d(0,-100%,0)
    &.fold-enter, &.fold-leave-to
      transform translate3d(0,0,0)
    .list-header
      height 40px
      padding 0 18px
      background-color #f3f5f7
      font-weight 200
      line-height 40px
      border-1px(rgba(7,17,27,0.1))
      .title
        float left
        font-size 14px
      .empty
        float right
        color rgb(0,160,220)
        font-size 12px
    .list-content
      padding 0 18px
      max-height 217px
      overflow hidden
      background-color #fff
      .list
        .food
          position relative
          font-size 14px
          line-height 24px
          height 24px
          padding 12px 0
          border-1px(rgba(7,17,27,0.1))
          &:last-child
            border-bottom 0px
          .name
            font-weight 200
          .price
            display inline-block
            position absolute
            right 90px
            bottom 12px
            margin 0 12px 0 18px
            font-weight 700
            color rgb(240,20,20)
        .cartcontrol-wrapper
          display inline-block
          position absolute
          right 0
          bottom 6px
.list-mask
  position fixed
  z-index 40
  left 0
  top 0
  width 100%
  height 100%
  background-color rgba(7,17,27,0.6)
  filter blur(10px)
  transition all .3s
  opacity 1
  &.blur-enter ,&.blur-leave-to
    opacity 0
</style>
