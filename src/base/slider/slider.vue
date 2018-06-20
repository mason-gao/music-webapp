<template>
  <div class="slider" ref="slider">
    <div class="slider-group" ref="sliderGroup">
      <slot></slot>
    </div>
    <div class="dots">
      <span class="dot" v-for="(item,index) in dots" :class="{active: currentIndex==index}"></span>
    </div>
  </div>  
</template>

<script type="text/ecmascript-6">
  import {addClass} from 'common/js/dom'
  import BScroll from 'better-scroll'

  export default {
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
        default: 4000
      }
    },
    data() {
      return {
        dots: [],
        currentIndex: 0
      }
    },
    mounted() {
      setTimeout(() => {
        this._setSliderWidth()
        this._setDots()
        this._initSlider()
      },20)
      window.addEventListener('resize', () => {   // 视口改变自适应
        if (!this.slider) {
          return
        }
        this._setSliderWidth(true)    // 重新设置宽度
        this.slider.refresh()       // 刷新轮播
      })
    },
    activated() {
      if (this.autoPlay) {
        this._play()
      }
    },
    deactivated() {
      clearTimeout(this.timer)
    },
    beforeDestroy() { // 在销毁之前清除计时器
      clearTimeout(this.timer)
    },
    methods: {
      _setSliderWidth(isResize) {
        this.children = this.$refs.sliderGroup.children  // 轮播图的个数
        let width = 0
        let sliderWidth = this.$refs.slider.clientWidth  // 视口大小
        for (let i = 0; i < this.children.length; i++) {
          let child = this.children[i]
          addClass(child, 'slider-item')     // 添加样式

          child.style.width = sliderWidth + 'px'  // 设置宽度
          width += sliderWidth    // 轮播总宽度
        }
        if (this.loop && !isResize) {
          width += 2 * sliderWidth      // 如果是循环播放，在首尾各多一个
        }
        this.$refs.sliderGroup.style.width = width + 'px'  // 设置轮播总宽度
      },
      _setDots() {
        this.dots = new Array(this.children.length)
      },
      _initSlider() {
        this.slider = new BScroll(this.$refs.slider,{
          scrollX: true,      // 横向滚动
          scrollY: false,
          momentum: true,
          click: true,
          snap: true,
          snapLoop: this.loop,
          snapThreshold: 0.3,
          snapSpeed: 400
        })
        this.slider.on("scrollEnd",() => {
          let pageIndex = this.slider.getCurrentPage().pageX  // 当前轮播序号
          
          if (this.loop) {
            pageIndex -= 1
          }
          this.currentIndex = pageIndex  // 设置小点激活状态
          if (this.autoPlay) {
            this._play()
          }
        })
        this.slider.on("beforeScrollStart",() => {
          if(this.autoPlay) {
            clearTimeout(this.timer)
          } 
        })
        if(this.autoPlay) {
          this._play()
        }
      },
      _play() {       // 轮播
        let pageIndex = this.currentIndex + 1
        if (this.loop) {    // 循环轮播是+2
          pageIndex += 1
        }
        this.timer = setTimeout(() => {
          this.slider.goToPage(pageIndex, 0, 400)  // 去到下一页
        }, this.interval)
      }
    }
  }
</script>

<style scoped lang="stylus" rel="stylesheet/stylus">
  @import "~common/stylus/variable"
  .slider
    position: relative
    min-height: 1px
    overflow: hidden
    .slider-group
      overflow: hidden
      .slider-item
        float: left
        a 
          display: block
          width: 100% 
        img
           display: block
           width: 100% 
    .dots
      position: absolute
      left: 0
      right: 0
      bottom: 12px
      text-align: center
      font-size: 0
      .dot
        display: inline-block
        width: 8px
        height: 8px
        margin: 0 4px
        border-radius: 50%
        background-color: $color-text-l
        &.active
          width: 20px
          border-radius: 5px
          background: $color-text-ll
</style>