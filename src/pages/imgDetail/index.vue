<template>
  <view>
    <view class="image_info">
      <view class="image_author_avatar">
        <image :src="imgDetail.user.avatar"></image>
      </view>
      <view class="image_author_info">
        <view class="image_author_name">{{ imgDetail.user.name }}</view>
        <view class="image_time">{{ imgDetail.cnTime }}</view>
      </view>
    </view>
    <view class="image_cover">
      <image :src="imgDetail.newThumb" mode="widthFix"></image>
    </view>
    <!-- 点赞开始 -->
    <view class="social">
      <view class="social_favs">
        <view class="iconfont icondianzan"></view>
        <view class="favs">{{ imgDetail.rank }}</view>
      </view>
      <view class="social_store">
        <view class="iconfont iconshoucang"></view>
        <view class="store">收藏</view>
      </view>
    </view>
    <!-- 点赞结束 -->
    <!-- 专辑开始 -->
    <view class="album_wrap">
      <view class="album_title">相关</view>
      <view class="album_list">
        <view class="album_item"> </view>
      </view>
    </view>
    <!-- 专辑结束 -->
  </view>
</template>

<script>
import moment from 'moment';
// 设置语言为中文
moment.locale('zh-cn');
export default {
  onLoad() {
    const { imgList, index } = getApp().globalData;
    this.imgDetail = imgList[index];
    this.imgDetail.newThumb =
      this.imgDetail.thumb + this.imgDetail.rule.replace('$<Height>', 360);
    this.imgDetail.cnTime = moment(this.imgDetail.atime * 1000).fromNow();
    this.getAlbumList();
  },

  data() {
    return {
      imgDetail: {},
      album: [],
    };
  },

  methods: {
    getAlbumList() {
      this.request({
        url: `http://157.122.54.189:9088/image/v2/wallpaper/wallpaper/${this.imgDetail.id}/comment`,
      }).then((res) => {
        console.log(res);
      });
    },
  },
};
</script>

<style lang="scss">
.image_info {
  display: flex;
  justify-content: flex-start;
  align-items: center;
  margin: 30rpx;
  .image_author_avatar {
    margin-right: 20rpx;
    image {
      border-radius: 50%;
      width: 100rpx;
      height: 100rpx;
    }
  }

  .image_author_info {
    .image_author_name {
      font-size: 34rpx;
      color: #000;
      font-weight: 600;
    }

    .image_time {
      font-size: 24rpx;
      color: #ccc;
    }
  }
}
.social {
  display: flex;
  justify-content: space-around;
  align-items: center;
  margin-top: 10rpx;
  border-bottom: 3rpx solid #eee;
  height: 80rpx;
  .social_favs {
    display: flex;
    justify-content: center;
    align-items: center;
    .iconfont {
    }
  }

  .social_store {
    display: flex;
    justify-content: center;
    align-items: center;
    .iconfont {
    }
  }
}
</style>
