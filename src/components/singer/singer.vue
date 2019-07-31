<template>
  <singer-list :data="singerList">
    <p>nothing</p>
    <div v-for="list in singerList" :key="list.title">
      <p>{{singerList.title}}</p>
      <ul>
        <li v-for="item in list.items" :key="item.title">
          <div>
            <img :src="item.singerPic" alt="">
          </div>
        </li>
      </ul>
    </div>
  </singer-list>
</template>

<script>
import axios from 'axios'
import Singer from 'common/js/singer'
import SingerList from 'base/singerList/singerList'

const HOT_SINGER_LEN = 20

export default {
  components: {
    SingerList
  },
  data() {
    return {
      singerIndex: [],
      singerList: []
    }
  },
  created() {
    setTimeout(() => {
      this.getSingerIndex()
    }, 20)
  },
  methods: {
    getSingerIndex() {
      let _params = {
        '-': 'getUCGI' + Math.floor(Math.random() * 100000000000000),
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
      axios.get('https://u.y.qq.com/cgi-bin/musicu.fcg', {params: _params})
        .then((res) => {
          let _data = res.data.SingerListSever.data
          this.getIndex(_data)
          console.log(this.singerList)
        })
        .catch((error) => {
          console.log(error)
        })
    },
    getSingerList(num) {
      let _params = {
        '-': 'getUCGI' + Math.floor(Math.random() * 10000000000000000),
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
              'index': num,
              'sin': 0,
              'cur_page': 1
            }
          }
        }
      }
      return axios.get('https://u.y.qq.com/cgi-bin/musicu.fcg', {params: _params})
    },
    getIndex(list) {
      Object.assign(this.singerIndex, list.tags.index)
      this.singerIndex.forEach((item) => {
        const key = item.name
        if (!this.singerList.key) {
          this.singerList[key] = {
            title: key,
            items: []
          }
          this.getSingerList(item.id)
            .then((res) => {
              let _data = res.data.SingerListSever.data
              if (!_data.singerlist.length) {
              } else {
                // 处理数据
                _data.singerlist.forEach((item, index) => {
                  if (index < HOT_SINGER_LEN) {
                    this.singerList[key].items.push(new Singer({
                      id: item.singer_id,
                      name: item.singer_name,
                      singerPic: item.singer_pic
                    }))
                  }
                })
              }
            })
            .catch((error) => {
              console.log(error)
            })
          console.log(this.singerList)
        }
      })
    }
  }
}
</script>

<style lang="stylus" scoped>

</style>
