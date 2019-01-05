<template>
  <div class="seller-wrapper" ref="sellerWrapper">
    <div>
      <div class="overhead">
        <div class="header">
          <div class="like-wrapper">
            <span class="icon-favorite"></span>
            <span>已收藏</span>
          </div>
          <h1 class="title">{{seller.name}}</h1>
          <div class="star-wrapper">
            <star
              class="star"
              :size="36"
              :score="seller.score"
            >
            </star>
            <span class="star-count">{{seller.ratingCount}}</span>
            <span class="sell-count">月售{{seller.sellCount}}单</span>
          </div>
        </div>
        <div class="head-desc">
          <div class="sell-price">
            <span class="count-per">起送价</span>
            <span class="count">{{seller.minPrice}}</span>
            <span class="unit">元</span>
          </div>
          <div class="delivery-price">
            <span class="count-per">商家配送</span>
            <span class="count">{{seller.deliveryPrice}}</span>
            <span class="unit">元</span>
          </div>
          <div class="delivery-time">
            <span class="count-per">平均配送时间</span>
            <span class="count">{{seller.deliveryTime}}</span>
            <span class="unit">分钟</span>
          </div>
        </div>
      </div>
      <div class="brand-wrapper">
        <h1 class="title">公告与活动</h1>
        <div class="brand-content">
          <p class="brand-desc">{{seller.bulletin}}</p>
          <ul class="support-list">
            <li
              v-for="support in seller.supports"
              :key="support.index"
              class="support"
            >
              <span :class="classMap[support.type]" class="icon"></span>
              <span class="support-desc">{{support.description}}</span>
            </li>
          </ul>
        </div>
      </div>
      <div class="scene-wrapper">
        <h1 class="title">商家实景</h1>
        <div class="scene-pitcher" ref="picWrapper">
          <ul class="pitcher-list" ref="picList">
            <li
              class="pitcher"
              :key="pitch.index"
              v-for="pitch in seller.pics"
            >
              <img :src="pitch" width="120px" height="90px">
            </li>
          </ul>
        </div>
      </div>
      <div class="desc-wrapper">
        <h1 class="title">商家信息</h1>
        <ul class="info-list">
          <li class="info" v-for="info in seller.infos" :key="info.index">
            <span class="text">{{info}}</span>
          </li>
        </ul>
      </div>
    </div>
    <shopcart
      :deliveryPrice="seller.deliveryPrice"
      :minPrice="seller.minPrice"
    >
    </shopcart>
  </div>
</template>

<script>
import star from '../star/star'
import BScroll from 'better-scroll'
import shopcart from '../shopcart/shopcart'

export default {
  components: {
    star,
    shopcart
  },
  props: {
    seller: {}
  },
  created () {
    this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee']
    this.$nextTick(() => {
      this.sellerScroll = new BScroll(this.$refs.sellerWrapper)
      this._initPics()
    })
  },
  methods: {
    _initPics () {
      if (this.seller.pics) {
        let picWidth = 120
        let margin = 6
        let width = (picWidth + margin) * this.seller.pics.length - margin
        this.$refs.picList.style.width = width + 'px'
        this.$nextTick(() => {
          if (!this.picScroll) {
            this.picScroll = new BScroll(this.$refs.picWrapper, {
              scrollX: true,
              eventPassthrough: 'vertical'
            })
          } else {
            this.picScroll.refresh()
          }
        })
      }
    }
  }
}
</script>

<style lang="stylus" scoped>
@import '../../common/stylus/index';

  .seller-wrapper
    position absolute
    top 174px
    bottom 46px
    left 0px
    right 0px
    overflow hidden
    background-color #f3f5f7
    .overhead
      background-color #fff
      position relative
      margin-bottom 16px
      padding 18px
      border-1px(rgba(7,17,27,0.1))
      .header
        padding-bottom 18px
        border-1px(rgba(7,17,27,0.1))
        .like-wrapper
          position absolute
          right 0
          top 0
          font-size 10px
          color rgb(77,85,93)
          line-height 10px
          text-align center
          .icon-favorite
            display block
            font-size 24px
            color rgb(240,20,20)
            line-height 24px
            margin-bottom 4px
        .title
          font-size 14px
          color rgb(7,17,27)
          line-height 14px
        .star-wrapper
            margin-top 8px
            line-height 18px
          .star
            display inline-block
            height 18px
          .star-count, .sell-count
            font-size 10px
            color rgb(77,85,93)
          .star-count
            margin 0 12px 0 8px
      .head-desc
        display flex
        padding-top 18px
        .sell-price, .delivery-price, .delivery-time
          flex 1
          text-align center
          .count-per
            display block
            font-size 10px
            color rgb(147,153,159)
            line-height 10px
            margin-bottom 4px
          .count
            font-size 24px
            font-weight 200
            color rgb(7,17,27)
            line-height 24px
          .unit
            font-size 10px
            font-weight 200
            color rgb(7,17,27)
            line-height 24px
        .sell-price, .delivery-price
          border-1px-right(rgba(7,17,27,0.1))
  .brand-wrapper
    padding 18px
    background-color #fff
    border-1px(rgba(7,17,27,0.1))
    margin-bottom 16px
    .title
      font-size 14px
      color rgb(7,17,27)
      line-height 14px
    .brand-content
      .brand-desc
        padding 0 12px
        margin 8px 0 16px 0
        font-size 12px
        font-weight 200
        color rgb(240,20,20)
        line-height 24px
      .support-list
        .support
          padding 16px 12px
          border-1px-top(rgba(7,17,27,0.1))
          .icon
            display inline-block
            margin-right 6px
            height 16px
            width 16px
            background-size 16px 16px
            background-repeat no-repeat
            vertical-align top
            &.decrease
              bg-image("decrease_1")
            &.discount
              bg-image("discount_1")
            &.guarantee
              bg-image("guarantee_1")
            &.invoice
              bg-image("invoice_1")
            &.special
              bg-image("special_1")
          .support-desc
            font-size 12px
            font-weight 200
            color rgb(7,17,27)
            line-height 16px
  .scene-wrapper
    margin-bottom 16px
    padding 18px
    background-color #fff
    border-1px(rgba(7,17,27,0.1))
    .title
      font-size 14px
      color rgb(7,17,27)
      line-height 14px
    .scene-pitcher
      margin-top 12px
      overflow: hidden
      white-space: nowrap
      .pitcher-list
        height 90px
        width 120px
        .pitcher
          margin-right 6px
          display inline-block
  .desc-wrapper
    padding 18px
    background-color #fff
    border-1px-top(rgba(7,17,27,0.1))
    border-1px(rgba(7,17,27,0.1))
    .title
      font-size 14px
      color rgb(7,17,27)
      line-height 14px
    .info-list
      margin-top 12px
      .info
        padding 16px 12px
        border-1px-top(rgba(7,17,27,0.1))
        &:last-child
          border-1px(rgba(7,17,27,0.1))
        .text
          font-size 12px
          font-weight 200
          color rgb(7,17,27)
          line-height 16px
</style>
