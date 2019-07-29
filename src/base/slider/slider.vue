<template>
  <div class="slide" ref="slide">
    <div class="slide-group" ref="slideGroup">
      <slot></slot>
    </div>
    <div class="dots">
      <span class="dot" v-for="(item, index) in dots" :key="index" :class="{active: currentPageIndex === index }"></span>
    </div>
  </div>
</template>

<script type="text/ecmasctipt-6">
import BScroll from 'better-scroll'
import {addClass} from 'common/js/dom'

export default {
  data() {
    return {
      dots: [],
      currentPageIndex: 0
    }
  },
  props: {
    loop: {
      type: Boolean,
      default: true
    },
    autoPlay: {
      type: Boolean,
      default: true
    },
    interval: {
      type: Number,
      default: 2000
    },
    showDot: {
      type: Boolean,
      default: true
    },
    threshold: {
      type: Number,
      drfault: 0.3
    },
    speed: {
      type: Number,
      default: 400
    }
  },
  mounted() {
    setTimeout(() => {
      // 初始化操作
      this._setSlideWidth()
      this._initDots()
      this._initSlide()
      if (this.autoPlay) {
        this._play()
      }
    }, 20)
    window.addEventListener('resize', () => {
      if (!this.slide) {
        return
      }
      this._setSlideWidth(true)
      this.slide.refresh()
    })
  },
  methods: {
    _setSlideWidth(isResize) {
      this.children = this.$refs.slideGroup.children
      let width = 0
      let slideWidth = this.$refs.slide.clientWidth
      for (let i = 0; i < this.children.length; i++) {
        let child = this.children[i]
        addClass(child, 'slide-item')
        child.style.width = slideWidth + 'px'
        width += slideWidth
      }
      if (this.loop && !isResize) {
        width += 2 * slideWidth
      }
      this.$refs.slideGroup.style.width = width + 'px'
    },
    _initSlide() {
      this.slide = new BScroll(this.$refs.slide, {
        scrollX: true,
        momentum: false,
        snap: {
          loop: this.loop,
          threshold: this.threshold,
          speed: this.speed
        }
      })
      this.slide.on('scrollEnd', () => {
        let pageIndex = this.slide.getCurrentPage().pageX
        this.currentPageIndex = pageIndex
        if (this.autoPlay) {
          clearTimeout(this.timer)
          this._play()
        }
      })
    },
    _play() {
      // let pageIndex = this.currentPageIndex + 1
      this.timer = setTimeout(() => {
        this.slide.next()
      }, this.interval)
    },
    _initDots() {
      this.dots = new Array(this.children.length)
    }
  },
  destroyed() {
    clearTimeout(this.timer)
  }
}
</script>

<style lang="stylus" scoped rel="stylesheet/stylus">
  @import "~common/stylus/variable"

  .slide
    min-height: 1px
    .slide-group
      overflow: hidden
      position: relative
      white-space: nowrap
      .slide-item
        float: left
        box-sizing: border-box
        overflow: hidden
        text-align: center
        a
          display: inline-block
          width: 100%
          overflow: hidden
          text-decoration: none
          height: 0
          padding-bottom: 40%
        img
          display: block
          width: 100%
    .dots
      position: absolute
      right: 0
      left: 0
      bottom: 12px
      text-align: center
      font-size: 0
      .dot
        display: inline-block
        margin: 0 4px
        width: 8px
        height: 8px
        border-radius: 50%
        background: $color-text-l
        &.active
          width: 20px
          border-radius: 5px
          background: $color-text-ll
</style>
