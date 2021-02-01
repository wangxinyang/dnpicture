<template>
  <scroll-view
    @scrolltolower="handleScroll"
    class="recommend_view"
    scroll-y
    v-if="recommends.length > 0"
  >
    <!-- 推荐开始 -->
    <view class="recommend_items">
      <navigator
        class="recommend_item"
        v-for="item in recommends"
        :key="item.id"
        :url="`/pages/album/index?id=${item.target}`"
      >
        <image :src="item.thumb" mode="widthFix"></image>
      </navigator>
    </view>
    <!-- 推荐结束 -->
    <!-- 月份开始 -->
    <view class="month_wrap">
      <view class="month_title">
        <view class="month_title_info">
          <view class="month">
            <text>{{ months.DD }}/</text>
            {{ months.MM }} 月
          </view>
          <view class="month_text">{{ months.title }}</view>
        </view>
        <view class="month_more">更多 > </view>
      </view>
      <view class="month_content">
        <view
          class="month_item"
          v-for="(item, index) in months.items"
          :key="item.id"
        >
          <go-detail :list="months.items" :index="index">
            <image
              :src="item.thumb + item.rule.replace('$<Height>', 360)"
              mode="aspectFill"
            ></image>
          </go-detail>
        </view>
      </view>
    </view>
    <!-- 月份结束 -->
    <!-- 热门开始 -->
    <view class="hot_wrap">
      <view class="hot_title">
        <text>热门</text>
      </view>
      <view class="hot_content">
        <view class="hot_item" v-for="(item, index) in vertical" :key="item.id">
          <go-detail :list="vertical" :index="index">
            <image :src="item.thumb" mode="widthFit"></image>
          </go-detail>
        </view>
      </view>
    </view>
    <!-- 热门结束 -->
  </scroll-view>
</template>

<script>
// 时间处理monent.js
import moment from 'moment';
import goDetail from '@/components/goDetail';
export default {
  mounted() {
    uni.setNavigationBarTitle({ title: '推荐' });
    this.getRecommendList();
  },

  components: {
    goDetail,
  },

  data() {
    return {
      recommends: [],
      months: {},
      vertical: [],
      params: {
        limit: 30,
        order: 'hot',
        skip: 0,
      },
      hasMore: true,
    };
  },
  methods: {
    // 获取推荐数据
    getRecommendList() {
      this.request({
        url: 'http://157.122.54.189:9088/image/v3/homepage/vertical',
        data: this.params,
      }).then((res) => {
        // 没有更多的数据了
        if (res.res.vertical.length === 0) {
          this.hasMore = false;
          return;
        }

        if (this.recommends.length === 0) {
          // 推荐图片
          this.recommends = res.res.homepage[1].items;
          // 月份
          this.months = res.res.homepage[2];
          this.months.DD = moment(this.months.stime).format('DD');
          this.months.MM = moment(this.months.stime).format('MM');
        }

        this.vertical = [...this.vertical, ...res.res.vertical];
      });
    },

    // 分页处理
    handleScroll() {
      if (this.hasMore) {
        this.params.skip += this.params.limit;
        this.getRecommendList();
      } else {
        uni.showToast({
          title: '没有更多的数据了',
          icon: 'none',
        });
      }
    },
  },
};
</script>

<style lang="scss">
.recommend_view {
  /* 可滚动区域是整个屏幕的高度-标题的高度 不需要包含底部的tabbar */
  height: calc(100vh - 36px);
}

.recommend_items {
  display: flex;
  flex-wrap: wrap;
  .recommend_item {
    width: 50%;
    border: 3rpx solid #fff;
  }
}
.month_wrap {
  .month_title {
    display: flex;
    justify-content: space-between;
    margin: 10rpx 30rpx;
    .month_title_info {
      display: flex;
      align-items: center;
      .month {
        color: $theme-color;
        font-size: 30rpx;
        font-weight: 600;
        text {
          font-size: 36rpx;
        }
      }

      .month_text {
        color: #000000;
        font-size: 34rpx;
        margin-left: 10rpx;
      }
    }

    .month_more {
      color: $theme-color;
      font-size: 24rpx;
    }
  }

  .month_content {
    display: flex;
    flex-wrap: wrap;
    .month_item {
      width: 33.33%;
    }
  }
}

.hot_wrap {
  .hot_title {
    padding: 20rpx;
    text {
      border-left: 20rpx solid $theme-color;
      font-size: 34rpx;
      font-weight: 600;
      padding-left: 20rpx;
    }
  }

  .hot_content {
    display: flex;
    flex-wrap: wrap;
    .hot_item {
      width: 33.33%;
      border: 5rpx solid #fff;
      image {
      }
    }
  }
}
</style>
