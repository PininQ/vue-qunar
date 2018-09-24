<template>
  <ul class="list">
    <li class="item"
      v-for="item of letters"
      :key="item"
      :ref="item"
      @touchstart="handleTouchStart"
      @touchmove="handleTouchMove"
      @touchend="handleTouchEnd"
      @click="handleLetterClick"
    >
      {{item}}
    </li>
  </ul>
</template>

<script>
export default {
  name: 'CityAlphabet',
  props: {
    cities: Object
  },
  computed: {
    letters () {
      const letters = []
      for (let i in this.cities) {
        letters.push(i)
      }
      return letters
    }
  },
  data () {
    return {
      touchStatus: false,
      startY: 0,
      timer: null
    }
  },
  updated () {
    /**
     * 数据发生变化（第一次加载 或 Ajax 获取到数据），Alphabet组件重新渲染，
     * 当该组件重新渲染之后，updated 生命周期钩子就会被执行，当数据渲染完毕之后，
     * 重新获取 A 字母所在的 DOM 对应的 offsetTop 值
     */
    this.startY = this.$refs['A'][0].offsetTop
  },
  methods: {
    handleLetterClick (e) {
      // 将用户点击的 字母 传递给父组件
      this.$emit('change', e.target.innerText)
    },
    handleTouchStart () {
      this.touchStatus = true
    },
    handleTouchMove (e) {
      /**
       * 函数节流，延迟 16ms 去执行手指的滚动，加入在这 16ms 之间重复进行手指的滚动，
       * 那么把上一次要做的操作清除，重新执行这次要做的事情
       */
      if (this.touchStatus) {
        if (this.timer) {
          clearTimeout(this.timer)
        }
        this.timer = setTimeout(() => {
          // A字母 距离顶部栏的距离 - 109
          // 这个值是固定的，可以写到钩子函数 updated 中
          // const startY = this.$refs['A'][0].offsetTop
          // console.log(startY)
          // 手指距离最顶部的高度 - 顶部栏的高度（63 + 36）
          const touchY = e.touches[0].clientY - 79
          // console.log(touchY)
          // 当前手指滑到的位置对应的字母下标（减去每个字母高度 20，再向下取整）
          const index = Math.floor((touchY - this.startY) / 20)
          // console.log(index)
          // 字母下标范围
          if (index >= 0 && index <= this.letters.length) {
            // 触发事件，传递 当前字母
            this.$emit('change', this.letters[index])
          }
        }, 16)
      }
    },
    handleTouchEnd () {
      this.touchStatus = false
    }
  }
}
</script>

<style lang="stylus" scoped>
@import '~styles/varibles.styl'
.list
  display flex
  flex-direction column
  justify-content center
  position absolute
  top 1.58rem
  right 0
  bottom 0
  width 0.4rem
  .item
    line-height 0.4rem
    text-align center
    color $bgColor
</style>
