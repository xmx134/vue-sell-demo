<template>
    <div class="ratings-content" ref="ratingsWrapper">
      <div>
        <div class="overview">
          <div class="ratingcount">
            <p class="count">{{seller.score}}</p>
            <p class="text">综合评价</p>
            <p class="desc">高于周边商家{{seller.rankRate}}%</p>
          </div>
          <div class="star-content">
            <div class="star-desc">
              <span>服务态度</span>
              <star :size="24" :score="seller.serviceScore" class="star"></star>
              <span class="star-count">{{seller.serviceScore}}</span>
            </div>
            <div class="star-desc">
              <span>食品评价</span>
              <star :size="24" :score="seller.foodScore" class="star"></star>
              <span class="star-count">{{seller.foodScore}}</span>
            </div>
            <div>
              <span>送达时间</span>
              <span class="time">{{seller.deliveryTime}}分钟</span>
            </div>
          </div>
        </div>
        <div class="ratings-wrapper">
          <ratingselect
            class="rating-select"
            :ratings="ratings"
            :selectType="selectType"
            :onlyContent="onlyContent"
            @only="Only"
            @select="Select"
          >
          </ratingselect>
          <div class="ratings-list">
            <ul>
              <li
                class="rating"
                v-for="rating in selectRatings"
                :key="rating.index"
              >
                <div class="time">{{rating.rateTime | formatTime}}</div>
                <img class="user-avatar" :src="rating.avatar" width="28px" height="28px">
                <div class="rating-content">
                  <div class="user">
                    <span class="name">{{rating.username}}</span>
                    <div class="star-desc">
                      <star class="user-star" :size="24" :score="rating.score"></star>
                      <span class="desc">{{rating.deliveryTime}}分钟送达</span>
                    </div>
                  </div>
                  <div class="rating-text">
                    <span class="text">{{rating.text}}</span>
                  </div>
                  <div class="spans">
                    <span :class="{'icon-thumb_up' : rating.rateType === 0, 'icon-thumb_down' : rating.rateType === 1}">
                    </span>
                    <span
                      v-for="recommend in rating.recommend"
                      :key="recommend.index"
                      class="food-span"
                    >
                      {{recommend}}
                    </span>
                  </div>
                </div>
              </li>
            </ul>
          </div>
        </div>
      </div>
      <shopcart
        :minPrice="seller.minPrice"
        :deliveryPrice="seller.deliveryPrice"
        :selectFoods="seller.selectFoods"
      >
      </shopcart>
    </div>
</template>

<script>
import BScroll from 'better-scroll'
import star from '../star/star'
import ratingselect from '../ratingselect/ratingselect'
import shopcart from '../shopcart/shopcart'
import { formatDate } from '../../common/js/date.js'

const ERR_NO = 0
const ALL = 2
/* eslint-disable */
const POSITIVE = 0
const NAGETIVE = 1

export default {
  components: {
    star,
    ratingselect,
    shopcart
  },
  props: {
    seller: Object,
  },
  data () {
    return {
      onlyContent: false,
      selectType: ALL,
      ratings: []
    }
  },
  filters: {
    formatTime (time) {
      let date = new Date(time)
      return formatDate(date, 'yyyy-MM-dd hh:mm')
    }
  },
  created () {
    this.$http.get('/api/ratings').then((Responed) => {
      Responed = Responed.body
      if (Responed.errno === ERR_NO) {
        this.ratings = Responed.data
        this.$nextTick(() => {
          this.ratingsScroll = new BScroll(this.$refs.ratingsWrapper, {
            click: true
          })
        })
      }
    })
  },
  methods: {
    Only () {
      this.onlyContent = !this.onlyContent
    },
    Select (type) {
      this.selectType = type
    }
  },
  computed: {
    onlyRatings () {
      if (this.onlyContent) {
        return this.ratings.filter((rating) => {
          return rating.text.length != 0
        })
      } else {
        return this.ratings
      }
    },
    selectRatings () {
      if (this.selectType === ALL) {
        return this.onlyRatings
      }
      if (this.selectType === POSITIVE) {
        return this.onlyRatings.filter((rating) => {
          return rating.rateType === 0
        })
      }
      if (this.selectType === NAGETIVE) {
        return this.onlyRatings.filter((rating) => {
          return rating.rateType === 1
        })
      }
    }
  }
}
</script>

<style lang="stylus" scoped>
@import '../../common/stylus/index.styl';

  .ratings-content
    position absolute
    top 174px
    bottom 46px
    left 0px
    right 0px
    overflow hidden
    background-color #f3f5f7
    .overview
      display flex
      padding 18px 0
      background-color #fff
      border-1px rgba(147,153,159,0.1)
      margin-bottom 18px
      .ratingcount
        flex 0 0 137px
        text-align center
        width 137px
        border-1px-right(rgba(7,17,27,0.1))
        .count
          font-size 24px
          color rgb(255,153,0)
          line-height 28px
          margin-bottom 6px
        .text
          font-size 12px
          line-height 12px
          color rgb(7,17,27)
          margin-bottom 8px
        .desc
          font-size 10px
          color rgb(147,153,159)
          line-height 10px
      .star-content
        flex 1
        padding 0 24px
        font-size 12px
        color rgb(7,17,27)
        line-height 18px
        .star-desc
          margin-bottom 8px
          .star
            display inline-block
            margin 0 12px
          .star-count
            font-size 12px
            color rgb(255,153,0)
            line-height 18px
        .time
          margin-left 12px
          font-size 12px
          color rgb(147,153,159)
          line-height 18px
    .ratings-wrapper
      padding 18px 24px
      background-color #fff
      .ratings-list
        .rating
          position relative
          padding 18px 0
          .time
            position absolute
            right 18px
            top 18px
            font-size 10px
            font-weight 200
            color rgb(147,153,159)
            line-height 12px
          .user-avatar
            position absolute
            left 0
            top 18px
            border-radius 50%
          .rating-content
            margin-left 40px
            .user
              font-size 10px
              line-height 12px
              .name
                color rgb(7,17,27)
                margin-bottom 4px
            .star-desc
              font-weight 200
              color rgb(147,153,159)
              .user-star
                display inline-block
                vertical-align top
                margin-right 6px
              .desc
                font-size 10px
                line-height 12px
            .rating-text
              margin 6px 0 8px 0
              .text
                font-size 12px
                color rgb(7,17,27)
                line-height 18px
            .spans
              .icon-thumb_up, .icon-thumb_down
                font-size 12px
                color rgb(0,160,220)
                line-height 16px
              .food-span
                padding 0 6px
                margin-left 8px
                font-size 9px
                color rgb(147,153,159)
                line-height 16px
                border-width 1px
                border-color rgba(7,17,27,0.1)
                border-radius 2px
                background-color #fff
</style>
