<template>
  <div class="cartcontrol">
    <transition name="move">
      <div
        v-show="food.count > 0"
        class="cart-decrease icon-remove_circle_outline"
        @click.stop.prevent="decreaseCount">
      </div>
    </transition>
    <div v-show="food.count > 0" class="cart-count">{{food.count}}</div>
    <div
      class="cart-add icon-add_circle"
      @click.stop.prevent="addCount">
    </div>
  </div>
</template>

<script>
import Vue from 'vue'
export default {
  props: {
    food: {
      type: Object
    }
  },
  methods: {
    addCount (event) {
      if (!this.food.count) {
        Vue.set(this.food, 'count', 1)
      } else {
        this.food.count += 1
      }
      this.$emit('add', event.target)
    },
    decreaseCount () {
      this.food.count += -1
      this.$emit('decrease')
    }
  }
}
</script>

<style lang="stylus">
.cartcontrol
  .cart-decrease
    display inline-block
    padding 6px
    transition all .4s linear
    font-size 24px
    color rgb(0,160,220)
    line-height 24px
    opacity 1
    transform translate3d(0,0,0) rotate(0)
    &.move-enter, &.move-leave-to
      opacity 0
      transform translate3d(24px,0,0) rotate(180deg)
  .cart-count
    display inline-block
    padding-top 6px
    vertical-align top
    width 12px
    font-size 10px
    color rgb(147,153,159)
    line-height 24px
  .cart-add
    display inline-block
    padding 6px
    font-size 24px
    color rgb(0,160,220)
    line-height 24px
</style>
