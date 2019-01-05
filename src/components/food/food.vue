<template>
  <transition name="move">
    <div v-show="showFlog" class="food" ref="foodWrapper">
      <div class="food-content">
        <div class="img-wrapper">
          <img :src="food.image" width="100%" height="100%">
          <div class="back">
            <i class="icon-arrow_lift" @click="hide"></i>
          </div>
        </div>
        <div class="title-wrapper">
          <h1 class="title">{{food.name}}</h1>
          <div class="desc">
            <span class="count">月售{{food.sellCount}}份</span>
            <span class="rating">好评率{{food.rating}}%</span>
          </div>
          <div class="price-wrapper">
            <span class="price-pre">￥</span>
            <span class="price">{{food.price}}</span>
            <span v-show="food.oldPrice" class="old-price">￥{{food.oldPrice}}</span>
          </div>
          <div class="cart-control-wrapper">
            <cartcontrol :food="food" @add="addCount"></cartcontrol>
          </div>
          <transition name="fade">
            <div class="buy" v-show="!food.count || food.count === 0" @click="add">加入购物车</div>
          </transition>
        </div>
        <div class="desc-wrapper">
          <h1 class="title">商品介绍</h1>
          <p class="desc">{{food.info}}</p>
        </div>
        <div class="rating-wrapper">
          <h1 class="title">商品评价</h1>
          <ratingselect
            :ratings="food.ratings"
            :desc="desc"
            :selectType="selectType"
            :onlyContent="onlyContent"
            @select="selected"
            @only="only"
          >
          </ratingselect>
          <div class="ratings-list">
            <ul v-show="food.ratings && food.ratings.length">
              <li
                v-for="rating in ratings"
                :key="rating.index"
                class="rating-item"
              >
                <div class="user">
                  <span class="name">{{rating.username}}</span>
                  <img :src="rating.avatar" class="avatar" width="12px" height="12px">
                </div>
                <div class="time">{{rating.rateTime | formatTime}}</div>
                <p class="text">
                  <span :class="{'icon-thumb_up' : rating.rateType === 0, 'icon-thumb_down' : rating.rateType === 1}">
                  </span>{{rating.text}}
                </p>
              </li>
            </ul>
            <div class="no-rating" v-show="!food.ratings || !food.ratings.length"></div>
          </div>
        </div>
      </div>
    </div>
  </transition>
</template>

<script>
import BScroll from 'better-scroll'
import cartcontrol from '../cartcontrol/cartcontrol'
import Vue from 'vue'
import ratingselect from '../ratingselect/ratingselect'
import { formatDate } from '../../common/js/date.js'

/* eslint-disable */
const POSITIVE = 0
const NAGETIVE = 1
const ALL = 2

export default {
  components: {
    cartcontrol,
    ratingselect
  },
  props: {
    food: {
      type: Object
    }
  },
  data () {
    return {
      showFlog: false,
      selectType: ALL,
      onlyContent: false,
      desc: {
        all: '全部',
        positive: '推荐',
        negative: '吐槽'
      }
    }
  },
  computed: {
    selectRatings () {
      if (this.selectType === ALL) {
        return this.food.ratings
      }
      if (this.selectType === POSITIVE) {
        return this.food.ratings.filter((rating) => {
          return rating.rateType === POSITIVE
        })
      }
      if (this.selectType === NAGETIVE) {
        return this.food.ratings.filter((rating) => {
          return rating.rateType === NAGETIVE
        })
      }
    },
    ratings () {
      if (this.onlyContent) {
        return this.selectRatings.filter((rating) => {
          return rating.text.length != 0
        })
      } else {
          return this.selectRatings
      }
    }
  },
  filters: {
    formatTime (time) {
      let date = new Date(time)
      return formatDate(date, 'yyyy-MM-dd hh:mm')
    }
  },
  methods: {
    selected (type) {
      this.selectType = type
    },
    only () {
      this.onlyContent = !this.onlyContent
    },
    addCount (target) {
      this.$emit('add', target)
    },
    add (event) {
      if (!event._constructed) {
        return
      }
      this.addCount(event.target)
      if (!this.food.count) {
        Vue.set(this.food, 'count', 1)
      } else {
        this.food.count += 1
      }
    },
    show () {
      this.showFlog = true
      this.selectType = ALL
      this.onlyContent = false
      if (!this.foodScroll) {
        this.foodScroll = new BScroll(this.$refs.foodWrapper, {
          click: true
        })
      }
    },
    hide () {
      this.showFlog = false
    }
  }
}
</script>

<style lang="stylus">
@import '../../common/stylus/index.styl';

.food
  position fixed
  left 0
  top 0
  bottom 48px
  z-index 30
  overflow hidden
  width 100%
  background-color #f3f5f7
  transition all 0.2s linear
  transform translate3d(0,0,0)
  .food-content
    .img-wrapper
      position relative
      width 100%
      height 0
      padding-bottom 100%
      img
        position absolute
        top 0
        left 0
      .back
        position absolute
        top 10px
        left 0
        .icon-arrow_lift
          display block
          padding 10px
          font-size 20px
          color #fff
    &.move-enter, &.move-leave-to
      transform translate3d(100%,0,0)
    .title-wrapper
      position relative
      margin-bottom 16px
      border-1px(rgba(7,17,27,0.1))
      padding 18px
      background-color #fff
      .title
        font-size 14px
        font-weight 700
        color rgb(7,17,27)
        line-height 14px
      .desc
        margin-top 8px
        font-size 10px
        color rgb(147,153,159)
        line-height 10px
        .count
          margin-right 12px
      .price-wrapper
        maring-top 18px
        .price-pre, .price
          color rgb(240,20,20)
          line-height 24px
        .price-pre
          font-size 10px
        .price
          font-size 14px
          font-weight 700
          margin-right 12px
        .old-price
          font-size 10px
          font-weight 700
          color rgb(147,153,159)
          text-decoration line-through
      .cart-control-wrapper
        display inline-block
        position absolute
        right 12px
        bottom 12px
      .buy
        display inline-block
        position absolute
        right 18px
        bottom 18px
        height 24px
        width 74px
        line-height 24px
        z-index 10
        text-align center
        background-color rgb(0,160,220)
        border-radius 12px
        font-size 10px
        color #fff
        transition all 0.2s linear
        opacity 1
        &.fade-enter, &.fade-leave-to
         opacity 0
    .desc-wrapper
      background-color #fff
      border-1px-top(rgba(7,17,27,0.1))
      border-1px(rgba(7,17,27,0.1))
      margin-bottom 16px
      padding 18px
      .title
        margin-bottom 6px
        font-weight 700
        font-size 14px
        line-height 14px
      .desc
        margin 0 8px
        font-size 12px
        font-weight 200
        color rgb(77,85,93)
        line-height 24px
    .rating-wrapper
      background-color #fff
      padding 18px
      .title
        margin-bottom 18px
        font-size 14px
        font-weight 700
        line-height 14px
      .ratings-list
        .rating-item
          position relative
          padding 16px 0
          border-1px(rgba(7,17,27,0.1))
          .user
            position absolute
            right 0px
            top 16px
            display inline
            .name
              display inline-block
              vertical-align top
              font-size 10px
              color rgb(147,153,159)
              line-height 12px
              margin-right 6px
            .avatar
              border-radius 50%
          .time
            font-size 10px
            color rgb(147,153,159)
            line-height 12px
            margin-bottom 6px
          .text
            font-size 12px
            color rgb(7,17,27)
            line-height 16px
            .icon-thumb_up, .icon-thumb_down
              font-size 12px
              line-height 24px
              margin-right 4px
            .icon-thumb_up
              color rgb(0,160,220)
            .icon-thumb_down
              color rgb(147,153,159)
</style>
