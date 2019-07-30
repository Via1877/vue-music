<template>
  <div class='recommend'>
    <scroll ref="scroll" class='recommend-content' :data="discList">
      <div>
        <div v-if="recommends.length" class='slider-wrapper'>
          <slider>
            <div v-for='(item,index) in recommends' :key='index'>
              <a :href='item.linkUrl'><img class="needclick" :src='item.picUrl' alt=''></a>
            </div>
          </slider>
        </div>
        <div v-if="discList.length" class='recommend-list'>
          <h1 class='list-title'>热门歌曲名单</h1>
          <ul>
            <li class="item" v-for="(item, index) in discList" :key="index">
              <div class="icon">
                <img v-lazy="item.imgurl" alt="" width="60px">
              </div>
              <div class="text">
                <h2 class="name" v-html="item.creator.name"></h2>
                <p class="desc" v-html="item.dissname"></p>
              </div>
            </li>
          </ul>
        </div>
      </div>
      <div class="loading-container" v-show="!discList.length">
        <loading></loading>
      </div>
    </scroll>
  </div>
</template>

<script>
import axios from 'axios'
import Slider from 'base/slider/slider'
import Scroll from 'base/scroll/scroll'
import Loading from 'base/loading/loading'

export default {
  components: {
    Slider,
    Scroll,
    Loading
  },
  data () {
    return {
      recommends: [],
      discList: []
    }
  },
  created () {
    this.getSlide()
    this.getDiscList()
  },
  methods: {
    getSlide () {
      axios.get('https://c.y.qq.com/musichall/fcgi-bin/fcg_yqqhomepagerecommend.fcg')
        .then((res) => {
          let str = JSON.stringify(res.data.data.slider)
          // console.log(typeof str)
          this.recommends = JSON.parse(str)
          // console.log(this.recommends[0].linkUrl)
        })
        .catch(function (error) {
          console.log(error)
        })
    },
    getDiscList () {
      axios.get('http://ustbhuangyi.com/music/api/getDiscList', {
        params: {
          notice: 0,
          platform: 'yqq.json',
          needNewCode: 0,
          categoryId: 10000000,
          sortId: 5,
          sin: 0,
          ein: 19,
          rnd: Math.random(),
          toStringg_tk: 5381,
          loginUin: 0,
          hostUin: 0,
          format: 'json',
          inCharset: 'utf8',
          outCharset: 'utf-8'
        }
      })
        .then((result) => {
          var res = result.data
          this.discList = res.data.list
        })
        .catch(function (error) {
          console.log(error)
        })
    }
  }
}
</script>

<style lang='stylus' scoped>
@import '~common/stylus/variable'

.recommend
  position: fixed
  width: 100%
  top: 88px
  bottom: 0
  .recommend-content {
    height: 100%
    overflow: hidden
    .slider-wrapper {
      position: relative
      width: 100%
      overflow: hidden
    }
    .recommend-list {
      .list-title {
        height: 65px
        line-height: 65px
        text-align: center
        font-size: $font-size-medium
        color: $color-theme
      }
      .item {
        display: flex
        box-sizing: border-box
        align-items: center
        padding: 0 20px 20px 20px
        .icon {
          flex: 0 0 60px
          width: 60px
          padding-right: 20px
        }
        .text {
          display: flex
          flex-direction: column
          justify-content: center
          flex: 1
          line-height: 20px
          overflow: hidden
          font-size: $font-size-medium
          .name {
            margin-bottom: 10px
            color: $color-text
          }
          .desc {
            color: $color-text-d
          }
        }
      }
    }
    .loading-container {
      position: absolute
      width: 100%
      top: 50%
      transform: translateY(-50%)
    }
  }
</style>
