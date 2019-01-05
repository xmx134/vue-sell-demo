<template>
    <div class="goods">
      <div class="menu-wrapper" ref="menuWrapper">
        <ul>
          <li v-for="(item, index) in goods" :key="item.index" class="menu-item" :class="{'current': currentIndex === index}" @click="selectMenu(index)">
            <span class="text">
              <span v-show="item.type > 0" class="icon" :class="classMap[item.type]"></span>{{item.name}}
            </span>
          </li>
        </ul>
      </div>
      <div class="foods-wrapper" ref="foodsWrapper">
        <ul>
          <li v-for="item in goods" :key="item.index" class="food-list food-list-hook" >
            <h1 class="title">{{item.name}}</h1>
            <ul>
              <li v-for="food in item.foods" :key="food.index" class="food-item" @click="selectFood(food)">
                <div class="icon">
                  <img width="57px" height="57px" :src="food.icon">
                </div>
                <div class="content">
                  <h2 class="name">{{food.name}}</h2>
                  <p class="desc">{{food.description}}</p>
                  <div class="extra">
                    <span class="count">月售{{food.sellCount}}份</span>
                    <span>好评率{{food.rating}}%</span>
                  </div>
                  <div class="price">
                    <span class="now-pre">￥</span>
                    <span class="now">{{food.price}}</span>
                    <span v-show="food.oldPrice" class="old">￥{{food.oldPrice}}</span>
                  </div>
                  <div class="cartcontral-wrapper">
                    <cartcontrol :food="food" @add="drop"></cartcontrol>
                  </div>
                </div>
              </li>
            </ul>
          </li>
        </ul>
      </div>
      <shopcart
        :deliveryPrice="seller.deliveryPrice"
        :minPrice="seller.minPrice"
        :selectFoods="selectFoods"
        ref="shopcart"
      ></shopcart>
      <food :food="selectedFood" ref="food" @add="drop"></food>
    </div>
</template>

<script>
import BScroll from 'better-scroll'
import shopcart from '../shopcart/shopcart'
import cartcontrol from '../cartcontrol/cartcontrol'
import food from '../food/food'

const ERR_OK = 0

export default {
  components: {
    shopcart,
    cartcontrol,
    food
  },
  props: {
    seller: {
      type: Object
    }
  },
  data () {
    return {
      goods: Object,
      classMap: [],
      listHeight: [],
      scrollY: 0,
      selectedFood: {}
    }
  },
  created () {
    this.$http.get('/api/goods').then((Response) => {
      Response = Response.body
      if (Response.errno === ERR_OK) {
        this.goods = Response.data
        this.$nextTick(() => {
          this._initScroll()
          this._calculateHeight()
        })
      }
    })
    this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee']
  },
  methods: {
    drop (target) {
      this.$nextTick(() => {
        this.$refs.shopcart._drop(target)
      })
    },
    _initScroll () {
      this.menuScroll = new BScroll(this.$refs.menuWrapper, {
        click: true
      })
      this.foodsScroll = new BScroll(this.$refs.foodsWrapper, {
        click: true,
        probeType: 3
      })
      setInterval(this.foodsScroll.on('scroll', (pos) => {
        this.scrollY = Math.abs(Math.round(pos.y))
      }), 200)
    },
    _calculateHeight () {
      let foodHeight = this.$refs.foodsWrapper.getElementsByClassName('food-list-hook')
      let height = 0
      this.listHeight.push(height)
      for (let i = 0; i < foodHeight.length; i++) {
        let item = foodHeight[i]
        height += item.clientHeight
        this.listHeight.push(height)
      }
    },
    selectMenu (index) {
      let foodList = this.$refs.foodsWrapper.getElementsByClassName('food-list-hook')
      let el = foodList[index]
      this.foodsScroll.scrollToElement(el, 300)
    },
    selectFood (food) {
      if (!event._constructed) {
        return
      }
      this.selectedFood = food
      this.$refs.food.show()
    },
    _drop (target) {
      this.$refs.shopcart.drop(target)
    }
  },
  computed: {
    currentIndex () {
      for (let i = 0; i < this.listHeight.length; i++) {
        let height1 = this.listHeight[i]
        let height2 = this.listHeight[i + 1]
        if (!height2 || (this.scrollY >= height1 && this.scrollY < height2)) {
          return i
        }
      }
      return 0
    },
    selectFoods () {
      let foods = []
      if (!this.goods[0]) {
        return []
      }
      this.goods.forEach((good) => {
        good.foods.forEach((food) => {
          if (food.count) {
            foods.push(food)
          }
        })
      })
      return foods
    }
  }
}
</script>

<style lang="stylus">
@import '../../common/stylus/index.styl';

.goods
  display flex
  position absolute
  top 174px
  bottom 46px
  left 0px
  right 0px
  overflow hidden
  .menu-wrapper
    flex 0 0 80px
    width 80px
    background-color rgba(7,17,27,0.1)
    .menu-item
      display table
      width 56px
      height 54px
      line-height 14px
      padding 0 12px
      &.current
        position relative
        z-index 10
        margin-top -1
        background-color #fff
        font-weight 700
        color rgb(240,20,20)
        .text
          border-none()
      .icon
        display inline-block
        width 12px
        height 12px
        background-size 12px 12px
        background-repeat no-repeat
        vertical-align top
        margin-right 2px
        &.decrease
          bg-image("decrease_3")
        &.discount
          bg-image("discount_3")
        &.guarantee
          bg-image("guarantee_3")
        &.invoice
          bg-image("invoice_3")
        &.special
          bg-image("special_3")
      .text
        display table-cell
        width 56px
        vertical-align middle
        border-1px(rgba(7,17,27,0.1))
        font-size 12px
        font-weight 200
        line-height 14px
  .foods-wrapper
    flex 1
    .title
      background-color #f3f5f7
      vertical-align center
      border-left 2px solid #d9dde1
      padding-left 14px
      font-size 12px
      color rgb(147,153,159)
      line-height 26px
      height 26px
    .food-item
      display flex
      border-1px(rgba(7,17,27,0.1))
      &:last-child
        border-none()
      .icon
        display inline-block
        flex 0 0 57px
        width 57px
        margin 18px 0 18px 18px
      .content
        display inline-block
        vertical-align top
        flex 1
        margin 20px 0 18px 10px
        font-size 10px
        color rgb(147,153,159)
        line-height 10px
        .name
          font-size 14px
          color rgb(7,17,27)
          line-height 14px
        .desc
          margin-top 8px
          font-size 10px
          color rgb(147,153,159)
          line-height 14px
        .extra
          margin-top 8px
          font-size 10px
          color rgb(147,153,159)
          line-height 10px
          .count
            margin-right 12px
        .price
          margin-top 8px
          font-size 10px
          font-weight 700
          line-height 24px
          .now-pre
            color rgb(240,20,20)
          .now
            font-size 14px
            color rgb(240,20,20)
            margin-right 8px
          .old
            color rgb(147,154,159)
            text-decoration line-through
        .cartcontral-wrapper
          position absolute
          right 0px
          bottom 0px
          margin 0 12px 12px 0
</style>
