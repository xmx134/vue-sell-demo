<template>
    <div class="header">
        <div class="content-wrapper">
            <div class="avatar">
              <img width="64" height="64" :src="seller.avatar">
            </div>
            <div class="content">
              <div class="title">
                <span class="brand"></span>
                <span class="name">{{seller.name}}</span>
              </div>
              <div class="description">
                {{seller.description}}/{{seller.deliveryTime}}分钟送达
              </div>
              <div v-if="seller.supports" class="support">
                <span class="icon" :class="classMap[seller.supports[0].type]"></span>
                <span class="text">{{seller.supports[0].description}}</span>
              </div>
              <div v-if="seller.supports" class="support-count" @click="ChangeDetail">
                <span class="count">{{seller.supports.length}}个</span>
                <i class="icon-keyboard_arrow_right"></i>
              </div>
            </div>
        </div>
        <div class="bulletin-wrapper" @click="ChangeDetail">
          <span class="bulletin-title"></span>
          <span class="bulletin-text">{{seller.bulletin}}</span>
          <i class="icon-keyboard_arrow_right"></i>
        </div>
        <div class="background">
          <img width="100%" height="100%" :src="seller.avatar">
        </div>
        <transition name="fade">
          <div v-show="detailShow" class="detail">
            <div class="detail-wrapper clearfix">
              <div class="detail-main">
                <h1 class="name">{{seller.name}}</h1>
                <div class="star-wrapper">
                  <star :score="seller.score" :size="48"></star>
                </div>
                <div class="title">
                  <div class="line"></div>
                  <div class="text">优惠信息</div>
                  <div class="line"></div>
                </div>
                <ul v-if="seller.supports" class="supports">
                  <li class="support-item" v-for="sellerItem in seller.supports" :key="sellerItem.index">
                    <span class="icon" :class="classMap[sellerItem.type]"></span>
                    <span class="text">{{sellerItem.description}}</span>
                  </li>
                </ul>
                <div class="title">
                  <div class="line"></div>
                  <div class="text">商家公告</div>
                  <div class="line"></div>
                </div>
                <div class="bulletin">
                  <p class="content">{{seller.bulletin}}</p>
                </div>
              </div>
            </div>
            <div class="detail-close">
              <i class="icon-close" @click="ChangeDetail"></i>
            </div>
          </div>
        </transition>
    </div>
</template>

<script>
import star from '../star/star.vue'

export default {
  components: {
    star
  },
  props: {
    seller: {
      type: Object
    }
  },
  data () {
    return {
      detailShow: false
    }
  },
  methods: {
    ChangeDetail () {
      this.detailShow = !this.detailShow
    }
  },
  created () {
    this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee']
  }
}
</script>

<style lang="stylus" scoped>
@import "../../common/stylus/mixin.styl";

  .header
    position relative
    overflow hidden
    color #fff
    background-color rgba(7,17,27,0.5)
    font-size 14px
    .content-wrapper
      position relative
      padding 24px 12px 18px 24px
      .avatar
        display inline-block
        vertical-align top
        img
          border-radius 2px
      .content
        margin-left 16px
        display inline-block
        .title
          margin-top 2px
          .brand
            display inline-block
            width 30px
            height 18px
            vertical-align top
            bg-image("brand")
            background-size 30px 18px
            background-repeat no-repeat
            margin-right 6px
          .name
            font-size 16px
            font-weight bold
            line-height 18px
        .description
          margin-top 8px
          font-size 12px
          font-weight 200
          line-height 12px
        .support
          margin 10px 0 2px 0
          line-height 12px
          .icon
            display inline-block
            width 12px
            height 12px
            vertical-align top
            background-size 12px 12px
            background-repeat no-repeat
            margin-right 4px
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
          .text
            font-size 10px
            font-weight 200
            line-height 12px
      .support-count
        position absolute
        right 12px
        bottom 14px
        padding 0px 8px
        height 24px
        line-height 24px
        border-radius 14px
        background-color: rgba(0,0,0,0.2)
        text-align center
        .count
          vertical-align top
          font-size 10px
        .icon-keyboard_arrow_right
          font-size 10px
          margin-left 2px
    .bulletin-wrapper
      position relative
      height 28px
      line-height 28px
      padding 0 22px 0 12px
      background-color rgba(7,17,27,0.2)
      white-space nowrap
      overflow hidden
      text-overflow ellipsis
      .bulletin-title
        display inline-block
        width 22px
        height 12px
        vertical-align top
        margin-top 7px
        bg-image(bulletin)
        background-size 22px 12px
        background-repeat no-repeat
      .bulletin-text
        margin-left 4px
        font-size 10px
        vertical-align top
      .icon-keyboard_arrow_right
        position absolute
        right 12px
        top 8px
        font-size 10px
    .background
      position absolute
      left 0
      top 0
      z-index -1
      width 100%
      height 100%
      filter blur(10px)
    .fade-enter, .fade-leave-to
      opacity 0
      background-color rgba(7,17,27,0)
    .fade-enter-to, .fade-leave
      opacity 1
      background-color rgba(7,17,27,0.8)
    .fade-enter-active, .fade-leave-active
      transition all .5s
    .detail
      position fixed
      left 0
      top 0
      z-index 100
      width 100%
      height 100%
      overflow auto
      background-color rgba(7,17,27,0.8)
      .detail-wrapper
        min-height 100%
        width 100%
        .detail-main
          margin-top 64px
          padding-bottom 64px
          .name
            height 16px
            line-height 16px
            font-size 16px
            font-weight 700
            text-align center
          .star-wrapper
            margin-top 18px
            padding 2px 0
            text-align center
          .title
            display flex
            width 80%
            margin 30px auto 24px
            .line
              flex 1
              position relative
              top -6px
              border-bottom 1px solid rgba(255,255,255,0.2)
            .text
              padding 0 12px
              font-size 14px
          .supports
            margin 24px auto 28px
            width 80%
            .support-item
              margin 0 12px 12px
              &:last-child
                margin-bottom 0
              .icon
                display inline-block
                vertical-align top
                width 16px
                height 16px
                margin-right 6px
                background-size 16px 16px
                background-repeat no-repeat
                &.decrease
                  bg-image("decrease_2")
                &.discount
                  bg-image("discount_2")
                &.guarantee
                  bg-image("guarantee_2")
                &.invoice
                  bg-image("invoice_2")
                &.special
                  bg-image("special_2")
              .text
                display inline-block
                font-size 12px
                font-weight 200
                line-height 16px
          .bulletin
            width 80%
            margin 24px auto 0
            .content
              padding 0 12px
              font-size 12px
              line-height 24px
              font-weight 200
      .detail-close
        position relative
        height 32px
        width 32px
        margin -64px auto 0 auto
        clear both
        font-size 32px
        color rgba(255,255,255,0.5)
</style>
