<template>
  <div>
    歌手
  </div>
</template>

<script>
import axios from 'axios'
import Singer from 'common/js/singer'

const HOT_SINGER_LEN = 10

export default {
  data() {
    return {
      singerList: [],
      singerIndex: []
    }
  },
  created() {
    setTimeout(() => {
      this.getSingerList()
    })
  },
  methods: {
    getSingerList() {
      axios.get('https://u.y.qq.com/cgi-bin/musicu.fcg', {
        params: {
          '-': 'getUCGI' + Math.random() * 100000000000000,
          g_tk: 5381,
          loginUin: 0,
          hostUin: 0,
          format: 'json',
          inCharset: 'utf8',
          outCharset: 'utf-8',
          notice: 0,
          platform: 'yqq.json',
          needNewCode: 0,
          data: {
            'comm': {
              'ct': 24,
              'cv': 0
            },
            'SingerListSever': {
              'module': 'Music.SingerListServer',
              'method': 'get_singer_list',
              'param': {
                'area': -100,
                'sex': -100,
                'genre': -100,
                'index': -100,
                'sin': 0,
                'cur_page': 1
              }
            }
          }
        }
      })
        .then((res) => {
          let _data = res.data.SingerListSever.data
          this._normalizeSinger(_data)
        })
        .catch((error) => {
          console.log(error)
        })
    },
    _normalizeSinger(list) {
      Object.assign(this.singerList, list.singerlist)
      Object.assign(this.singerIndex, list.tags.index)
      console.log(this.singerList)
      let map = {
        hot: {
          title: this.singerIndex.name,
          items: []
        }
      }
      this.singerList.forEach((item, index) => {
        if (index < HOT_SINGER_LEN) {
          map.hot.items.push(new Singer({
            id: item.singer_id,
            name: item.singer_name,
            singerPic: item.singer_pic
          }))
        }
      })
      console.log(map)
    }
  }
}
</script>

<style lang="stylus" scoped>

</style>
