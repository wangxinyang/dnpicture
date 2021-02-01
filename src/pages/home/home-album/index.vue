<template>
  <scroll-view class="album_view" @scrolltolower="handleScroll" scroll-y>
    <!-- 轮播图开始 -->
    <view class="banner_swiper">
      <swiper autoplay indicator-dots circular>
        <swiper-item v-for="item in banner" :key="item.id">
          <image :src="item.thumb"></image>
        </swiper-item>
      </swiper>
    </view>
    <!-- 轮播图结束 -->
    <!-- 列表开始 -->
    <view class="album_list">
      <navigator
        class="album_item"
        v-for="item in album"
        :key="item.id"
        :url="`/pages/album/index?id=${item.id}`"
      >
        <view class="album_pic">
          <image :src="item.cover" mode="aspectFill"></image>
        </view>
        <view class="album_info">
          <view class="album_name">{{ item.name }}</view>
          <view class="album_desc">{{ item.desc }}</view>
          <view class="album_btn">
            <view class="album_attention">关注</view>
          </view>
        </view>
      </navigator>
    </view>
    <!-- 列表结束 -->
  </scroll-view>
</template>

<script>
export default {
  data() {
    return {
      params: {
        limit: 30,
        order: 'new',
        skip: 0,
      },
      banner: [],
      album: [],
      hasMore: true,
    };
  },
  mounted() {
    uni.setNavigationBarTitle({ title: '专辑' });
    this.getList();
  },
  methods: {
    getList() {
      this.request({
        url: 'http://157.122.54.189:9088/image/v1/wallpaper/album',
        data: this.params,
      }).then((res) => {
        console.log(res);
        if (res.res.album.length === 0) {
          this.hasMore = false;
          return;
        }

        if (this.banner.length === 0) {
          this.banner = res.res.banner;
        }

        this.album = [...this.album, ...res.res.album];
      });
    },
    handleScroll() {
      if (this.hasMore) {
        this.params.skip += this.params.limit;
        this.getList();
      } else {
        uni.showToast({
          title: '没有更多的数据',
          icon: 'none',
        });
      }
    },
  },
};
</script>

<style lang="scss">
.album_view {
  height: calc(100vh - 36px);
}

.banner_swiper {
  swiper {
    height: calc(750rpx / 2.3);
    image {
      height: 100%;
    }
  }
}
.album_list {
  padding: 10rpx;
  .album_item {
    padding: 10rpx 0;
    display: flex;
    border-bottom: 1rpx solid #ccc;
    .album_pic {
      flex: 1;
      padding: 10rpx;
      image {
        width: 200rpx;
        height: 200rpx;
      }
    }

    .album_info {
      flex: 2;
      padding: 0 10rpx;
      /* 这里flex会被不换行的文字将宽度撑开 所以这里设置超出的隐藏 */
      overflow: hidden;
      .album_name {
        font-size: 30rpx;
        color: #000;
        padding: 10rpx 0;
      }

      .album_desc {
        padding: 10rpx 0;
        font-size: 24rpx;
        /* 三行代码实现文字显示一行 最后显示三个点 */
        text-overflow: ellipsis;
        overflow: hidden;
        white-space: nowrap;
      }

      .album_btn {
        display: flex;
        justify-content: flex-end;
        .album_attention {
          padding: 10rpx;
          color: $theme-color;
          border: 1rpx solid $theme-color;
        }
      }
    }
  }
}
</style>
