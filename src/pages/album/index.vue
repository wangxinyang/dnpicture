<template>
  <view class="album_detail_wrap">
    <view class="album_detail_wallpaper">
      <image :src="album.cover" mode="widthFit"></image>
      <view class="album_options">
        <view class="album_name">{{ album.name }}</view>
        <view class="album_btn">
          <text class="album_attention">
            关注专辑
          </text>
        </view>
      </view>
    </view>
    <view class="album_detail_desc">
      <view class="album_author">
        <image :src="album.user.avatar"></image>
        <view class="album_author_name">{{ album.user.name }}</view>
      </view>
      <view class="album_desc">
        <text>{{ album.desc }}</text>
      </view>
    </view>
    <view class="album_detail_pics">
      <view class="pics_item" v-for="(item, index) in wallpaper" :key="item.id">
        <go-detail :list="wallpaper" :index="index">
          <image
            mode="widthFix"
            :src="item.thumb + item.rule.replace('$<Height>', 360)"
          ></image>
        </go-detail>
      </view>
    </view>
  </view>
</template>

<script>
import goDetail from '@/components/goDetail';
export default {
  components: {
    goDetail,
  },

  data() {
    return {
      params: {
        limit: 30,
        order: 'new',
        skip: 0,
        first: 1,
      },
      album: {},
      wallpaper: [],
      id: 1,
      hasMore: true,
    };
  },

  onLoad(options) {
    this.id = options.id;
    // this.id = '5d5f8e45e7bce75ae7fb8278';
    this.getList();
  },
  // 窗口滚动到底的操作
  onReachBottom() {
    if (this.hasMore) {
      this.params.first = 0;
      this.params.skip += this.params.limit;
      this.getList();
    } else {
      uni.showToast({
        title: '没有更多的数据',
        icon: 'none',
      });
    }
  },

  methods: {
    getList() {
      this.request({
        url: `http://157.122.54.189:9088/image/v1/wallpaper/album/${this.id}/wallpaper`,
        data: this.params,
      }).then((res) => {
        if (Object.keys(this.album).length === 0) {
          this.album = res.res.album;
        }
        if (res.res.wallpaper.length === 0) {
          this.hasMore = false;
          uni.showToast({
            title: '没有更多数据了',
            icon: 'none',
          });
          return;
        }
        this.wallpaper = [...this.wallpaper, ...res.res.wallpaper];
      });
    },
  },
};
</script>

<style lang="scss">
.album_detail_wrap {
  .album_detail_wallpaper {
    position: relative;
    image {
      height: 420rpx;
    }

    .album_options {
      position: absolute;
      width: 100%;
      bottom: 20rpx;
      display: flex;
      justify-content: space-around;
      .album_name {
        color: #fff;
      }

      .album_btn {
        background-color: $theme-color;
        border-radius: 5rpx;
        text.album_attention {
          color: #fff;
          padding: 10rpx;
        }
      }
    }
  }

  .album_detail_desc {
    margin: 20rpx;
    .album_author {
      display: flex;
      justify-content: flex-start;
      image {
        width: 50rpx;
        height: 50rpx;
      }

      .album_author_name {
        padding-left: 10rpx;
      }
    }

    .album_desc {
      margin-top: 10rpx;
    }
  }

  .album_detail_pics {
    display: flex;
    flex-wrap: wrap;
    .pics_item {
      width: 33.33%;
      border: 3rpx solid #fff;
      image {
      }
    }
  }
}
</style>
