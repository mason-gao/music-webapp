<template>
  <div class="recommend">
    <scroll class="recommend-content" :data="discList" ref="scroll">
      <div>
        <template v-if="recommends.length">
          <slider>
            <div v-for="item in recommends">
              <a :href="item.linkUrl">
                <img :src="item.picUrl" @load="loadImage">
              </a>
            </div>
          </slider>
        </template> 
        <div class="recommend-list">
          <h2 class="list-title">热门歌单推荐</h2>
          <ul>
            <li class="item" v-for="item in discList" @click="selectItem(item)">
              <div class="icon">
                <img width="60" height="60" v-lazy="item.imgurl" >
              </div>
              <div class="text">
                <h3 class="name" v-html="item.creator.name"></h3>
                <p class="desc" v-html="item.dissname"></p>
              </div>
            </li>
          </ul>
        </div>
      </div>
      <div class="loading-wrapper" v-show="!discList.length">
        <loading></loading>
      </div>
    </scroll>
    <router-view></router-view>
  </div>
</template>

<script type="text/ecmascript-6">
  import Slider from 'base/slider/slider'
  import {getRecommend,getDiscList} from 'api/recommend'
  import Scroll from 'base/scroll/scroll'
  import Loading from 'base/loading/loading'
  import {mapMutations} from 'vuex'
  
  export default {
    data() {
      return {
        recommends: [],
        discList: []
      }
    },
    components: {
      Slider,
      Scroll,
      Loading
    },
    methods: {
      loadImage() { // 图片加载完成后，让scroll组件重新计算高度
        if (!this.checkloaded) {
          this.checkloaded = true
          this.$refs.scroll.refresh()
        }
      },
      selectItem(item) {  // 具体推荐歌曲的详情页路由
        this.$router.push({
          path: `/recommend/${item.dissid}`
        })
        this.setDisc(item)
      },
      ...mapMutations({
        setDisc: 'SET_DISC'
      })
    },
    created() {
      getRecommend().then((res) => {
          if (res.code === 0) {
            this.recommends = res.data.slider
          }
      })     
      getDiscList().then((res) => {
        if (res.code === 0) {
          this.discList = res.data.list
        }
      })        
    }
  }
</script>

<style scoped lang="stylus" rel="stylesheet/stylus">
  @import "~common/stylus/variable"

  .recommend
    position: fixed
    top: 88px
    bottom: 0
    width: 100%
    .recommend-content
      height: 100%
      overflow: hidden
      .recommend-list
        .list-title
          height: 65px
          line-height: 65px
          text-align: center
          font-size: $font-size-medium
          color: $color-theme
        .item
          display: flex
          box-sizing: border-box
          padding: 0 20px 20px 20px
          .icon
            flex: 0 0 60px
            width: 60px
            padding-right: 20px
            img
              display: block
          .text
            display: flex
            flex-direction: column
            justify-content: center
            flex: 1
            line-height: 20px
            font-size: $font-size-medium
            .name
              margin-bottom: 10px
              color: $color-text
            .desc
              color: $color-text-d
      .loading-wrapper
        position: absolute
        top: 50%
        left: 0
        width: 100%
        transform: translateY(-50%)
        
</style>