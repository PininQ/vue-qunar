<template>
  <div class="wrapper">
    <!-- 轮播图 -->
    <swiper :options="swiperOption" v-if="showSwiper">
      <swiper-slide v-for="item of list" :key="item.id">
        <img class="swiper-img" :src="item.imgUrl" />
      </swiper-slide>
      <div class="swiper-pagination" slot="pagination"></div>
    </swiper>
  </div>
</template>

<script>
export default {
  name: 'HomeSwiper',
  props: {
    list: Array
  },
  data () {
    return {
      swiperOption: {
        pagination: '.swiper-pagination',
        loop: true, // 循环切换
        autoplay: 1800,
        disableOnInteraction: false, // 触碰后自动切换也不会停止
        autoplayDisableOnInteraction: false, // 解决swiper手动改滑动之后不再自动滑动问题
        observer: true, // 修改swiper的子元素时，自动初始化swiper
        observeParents: true // 将observe应用于Swiper的父元素，与上面一起使用
      }
    }
  },
  computed: {
    showSwiper () {
      return this.list.length
    }
  }
}
</script>

<style lang="stylus" scoped>
.wrapper >>> .swiper-pagination-bullet-active
  background #fff !important
.wrapper
  overflow hidden
  width 100%
  height 0
  padding-bottom 26.67%
  background #eee
  .swiper-img
    width 100%
</style>
