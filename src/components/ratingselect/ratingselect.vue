<template>
  <div class="ratings">
    <div class="rating-span">
      <span
        class="all"
        :class="{'choose' : selectType === 2}"
        @click="select(2, $event)"
      >
        {{desc.all}}
        <span class="number">{{ratings.length}}</span>
      </span>
      <span
        class="positive"
        :class="{'choose' : selectType === 0}"
        @click="select(0, $event)"
      >
        {{desc.positive}}
        <span class="number">{{positive.length}}</span>
      </span>
      <span
        class="negative"
        :class="{'choose' : selectType === 1}"
        @click="select(1, $event)"
      >
        {{desc.negative}}
        <span class="number">{{ratings.length - positive.length}}</span>
      </span>
    </div>
    <div class="selectType" :class="{'on' : onlyContent}" @click="Only">
      <i class="icon-check_circle"></i>
      <span class="text">只看有内容的评价</span>
    </div>
  </div>
</template>

<script>
/* eslint-disable */
const POSITIVE = 0
const NEGATIVE = 1
const ALL = 2

export default {
  props: {
    ratings: {
      type: Array,
      default () {
        return []
      }
    },
    selectType: {
      type: Number,
      default: ALL
    },
    onlyContent: {
      type: Boolean,
      default: false
    },
    desc: {
      type: Object,
      default () {
        return {
          all: '全部',
          positive: '满意',
          negative: '不满意'
        }
      }
    }
  },
  computed: {
    positive () {
      return this.ratings.filter((rating) => {
        return rating.rateType === POSITIVE
      })
    }
  },
  methods: {
    select (type, event) {
      if (!event._constructed) {
        return
      }
      this.$emit('select', type)
    },
    Only (event) {
      if (!event._constructed) {
        return
      }
      this.$emit('only')
    }
  }
}
</script>

<style lang="stylus">
@import '../../common/stylus/index.styl';

.ratings
  position relative
  .rating-span
    height 32px
    line-height 16px
    .all, .positive, .negative
      padding 8px 12px
      border-radius 1px
      font-size 12px
      line-height 16px
      vertical-align middle
    .all, .positive, .negative
      color rgb(77,85,93)
      .number
        font-size 8px
        margin-left 3px
      &.choose
        color #fff
    .all
      background-color rgba(0,160,220,0.2)
      margin-right 8px
      &.choose
        background-color rgb(0,160,220)
    .positive
      background-color rgba(0,160,220,0.2)
      margin-right 8px
      &.choose
        background-color rgb(0,160,220)
    .negative
      background-color rgba(77,85,93,0.2)
      &.choose
        background rgb(77,85,93)
  .selectType
    padding 12px 0
    line-height 24px
    color rgb(147,153,159)
    border-1px(rgba(7,17,27,0.1))
    border-1px-top(rgba(7,17,27,0.1))
    &.on
      .icon-check_circle
        color #00c850
    .icon-check_circle
      display inline-block
      vertical-align top
      font-size 24px
    .text
      margin-left 4px
      font-size 12px
</style>
