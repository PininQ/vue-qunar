<template>
  <div>
    <div class="search">
      <input v-model="keyword" class="search-input" type="text" placeholder="输入城市名或拼音">
    </div>
    <div class="search-content" ref="search" v-show="keyword">
      <ul>
        <li
          class="search-item border-bottom"
          v-for="item of list"
          :key="item.id"
          @click="handleCityClick(item.name)"
        >
          {{item.name}}
        </li>
        <li class="search-item border-bottom" v-show="hasNoData">
          没有找到匹配数据
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
import Bscroll from 'better-scroll'
import { mapMutations } from 'vuex'
export default {
  name: 'CitySearch',
  props: {
    cities: Object
  },
  data () {
    return {
      keyword: '',
      list: [],
      timer: null
    }
  },
  computed: {
    hasNoData () {
      return !this.list.length
    }
  },
  watch: {
    // 节流函数：监听 keyword 改变
    keyword () {
      if (this.timer) {
        clearTimeout(this.timer)
      }
      // 如果没有关键词，则把搜索结果置为空数组
      if (!this.keyword) {
        this.list = []
        return
      }
      // keyword 改变后，延迟 100ms 执行
      this.timer = setTimeout(() => {
        const result = []
        // 对每一个 cities 进行遍历
        for (let i in this.cities) {
          // 对 cities 中的每一个键值对的值（spell和name）再进行一次遍历
          this.cities[i].forEach(value => {
            // 从 spell 和 name 中搜索关键词
            if (value.spell.indexOf(this.keyword) > -1 ||
                value.name.indexOf(this.keyword) > -1) {
              // 把搜索到的关键词添加到 result 中
              result.push(value)
            }
            // 将所有搜索到结果存到 list 中
            this.list = result
          })
        }
      }, 100)
    }
  },
  methods: {
    handleCityClick (city) {
      this.changeCity(city)
      this.$router.push('/')
    },
    ...mapMutations(['changeCity'])
  },
  mounted () {
    this.scroll = new Bscroll(this.$refs.search)
  }
}
</script>

<style lang="stylus" scoped>
@import '~styles/varibles.styl'
.search
  height .72rem
  padding 0 .1rem
  background $bgColor
  .search-input
    box-sizing border-box
    width 100%
    height .62rem
    padding 0 .2rem
    line-height .62rem
    text-align center
    border-radius .3rem
    color #666
.search-content
  z-index 1
  overflow hidden
  position absolute
  top 1.58rem
  left 0
  right 0
  bottom 0
  background #eee
  .search-item
    line-height .62rem
    padding-left .2rem
    background #fff
    color #666
</style>
