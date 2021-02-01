<template>
  <view @click="handleClick">
    <slot></slot>
  </view>
</template>

<script>
export default {
  props: {
    list: Array,
    index: Number,
  },

  methods: {
    handleClick() {
      // 1.缓存数据到全局变量中
      getApp().globalData.imgList = this.list;
      getApp().globalData.index = this.index;
      // 2.跳转图片详情页面
      uni.navigateTo({
        url: '/pages/imgDetail/index',
      });
    },
  },
};
</script>

<style lang="scss"></style>
