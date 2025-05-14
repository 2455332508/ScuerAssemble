<template>
  <view class="dorm-container">
    <!-- 顶部导航栏 -->
    <view class="nav-bar">
      <view class="left">
        <text class="logo">🌐</text>
        <text class="username">姓名</text>
      </view>
      <view class="center">
        <text class="area">xx校区xx田舍</text>
      </view>
      <view class="right">
        <uni-icons type="search" size="22" color="#fff"></uni-icons>
      </view>
    </view>

    <!-- 功能区 -->
    <view class="function-card">
      <view class="function-item" v-for="(item, idx) in functions" :key="idx">
        <view class="icon-placeholder">{{ item.icon }}</view>
        <text class="function-text">{{ item.text }}</text>
      </view>
    </view>

    <!-- 分类标签 -->
    <view class="category-bar">
      <text :class="['category', currentCategory === 'latest' ? 'active' : '']" @click="currentCategory = 'latest'">最新</text>
      <text :class="['category', currentCategory === 'hot' ? 'active' : '']" @click="currentCategory = 'hot'">热门</text>
    </view>

    <!-- 帖子列表 -->
    <view class="post-list">
      <view class="post-item">
        <view class="post-title">
          <text class="hot-tag">热</text>
          四川大学校招会正在火热进行中！！！
        </view>
        <view class="post-content">
          <text>由于毕业季靠近，学校xxxxxx......</text>
          <image class="post-img" src="https://img2.baidu.com/it/u=1234567890,1234567890&fm=253&fmt=auto&app=138&f=JPEG?w=500&h=333"></image>
        </view>
        <view class="post-info">
          <image class="avatar" src="https://img2.baidu.com/it/u=1234567890,1234567890&fm=253&fmt=auto&app=138&f=JPEG?w=60&h=60"></image>
          <text class="author">天成小王子</text>
          <text class="time">12月13日 23:22</text>
          <uni-icons type="eye" size="16" color="#bbb"></uni-icons>
          <text class="views">5692</text>
        </view>
      </view>
    </view>

    <!-- 加载中 -->
    <view class="loading">正在加载中...</view>

    <!-- 底部导航栏 -->
    <view class="tab-bar">
      <view class="tab-item active">
        <uni-icons type="home" size="24" color="#1abc9c"></uni-icons>
        <text>首页</text>
      </view>
      <view class="tab-item">
        <uni-icons type="grid" size="24"></uni-icons>
        <text>田舍</text>
      </view>
      <view class="tab-item publish-btn">
        <view class="circle">
          <uni-icons type="plus" size="32" color="#fff"></uni-icons>
        </view>
        <text>发布</text>
      </view>
      <view class="tab-item">
        <uni-icons type="chatbubble" size="24"></uni-icons>
        <text>消息</text>
        <view class="badge">99</view>
      </view>
      <view class="tab-item">
        <uni-icons type="person" size="24"></uni-icons>
        <text>我的</text>
      </view>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      currentCategory: 'latest',
      functions: [
        { icon: '✖️', text: '田舍活动' },
        { icon: '🖼️', text: '田舍风采' },
        { icon: '✔️', text: '田舍公告' }
      ]
    }
  }
}
</script>

<style lang="scss">
.dorm-container {
  min-height: 100vh;
  background: #f5f5f5;
  padding-bottom: 120rpx;
}
.nav-bar {
  background: #409eff;
  color: #fff;
  height: 100rpx;
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 0 24rpx;
  .left {
    display: flex;
    align-items: center;
    .logo {
      font-size: 36rpx;
      margin-right: 10rpx;
    }
    .username {
      font-size: 28rpx;
      font-weight: bold;
    }
  }
  .center {
    font-size: 28rpx;
    font-weight: 500;
  }
  .right {
    display: flex;
    align-items: center;
  }
}
.function-card {
  background: #fff;
  margin: 24rpx 16rpx 0 16rpx;
  border-radius: 16rpx;
  display: flex;
  justify-content: space-around;
  align-items: center;
  padding: 24rpx 0;
  .function-item {
    display: flex;
    flex-direction: column;
    align-items: center;
    .icon-placeholder {
      width: 60rpx;
      height: 60rpx;
      border-radius: 50%;
      background: #f0f0f0;
      display: flex;
      align-items: center;
      justify-content: center;
      font-size: 36rpx;
      margin-bottom: 8rpx;
    }
    .function-text {
      font-size: 24rpx;
      color: #333;
    }
  }
}
.category-bar {
  display: flex;
  padding: 0 24rpx;
  margin-top: 16rpx;
  .category {
    font-size: 28rpx;
    color: #888;
    margin-right: 32rpx;
    position: relative;
    &.active {
      color: #409eff;
      font-weight: bold;
      &::after {
        content: '';
        display: block;
        width: 100%;
        height: 4rpx;
        background: #409eff;
        border-radius: 2rpx;
        position: absolute;
        left: 0;
        bottom: -8rpx;
      }
    }
  }
}
.post-list {
  margin: 16rpx 16rpx 0 16rpx;
  .post-item {
    background: #fff;
    border-radius: 12rpx;
    padding: 20rpx;
    margin-bottom: 16rpx;
    .post-title {
      font-size: 28rpx;
      font-weight: bold;
      color: #222;
      display: flex;
      align-items: center;
      .hot-tag {
        background: #ff4d4f;
        color: #fff;
        font-size: 20rpx;
        border-radius: 6rpx;
        padding: 2rpx 10rpx;
        margin-right: 10rpx;
      }
    }
    .post-content {
      display: flex;
      align-items: center;
      margin: 12rpx 0;
      font-size: 26rpx;
      color: #555;
      .post-img {
        width: 100rpx;
        height: 80rpx;
        border-radius: 8rpx;
        margin-left: 16rpx;
        object-fit: cover;
      }
    }
    .post-info {
      display: flex;
      align-items: center;
      font-size: 22rpx;
      color: #aaa;
      .avatar {
        width: 36rpx;
        height: 36rpx;
        border-radius: 50%;
        margin-right: 8rpx;
      }
      .author {
        color: #888;
        margin-right: 12rpx;
      }
      .time {
        margin-right: 12rpx;
      }
      .views {
        margin-left: 4rpx;
      }
    }
  }
}
.loading {
  text-align: center;
  color: #888;
  font-size: 24rpx;
  margin: 20rpx 0 120rpx 0;
}
.tab-bar {
  position: fixed;
  left: 0;
  right: 0;
  bottom: 0;
  height: 100rpx;
  background: #fff;
  display: flex;
  justify-content: space-around;
  align-items: flex-end;
  border-top: 1px solid #eee;
  z-index: 100;
  .tab-item {
    flex: 1;
    display: flex;
    flex-direction: column;
    align-items: center;
    font-size: 20rpx;
    color: #888;
    position: relative;
    &.active {
      color: #1abc9c;
      text {
        color: #1abc9c;
      }
    }
    .badge {
      position: absolute;
      top: 4rpx;
      right: 24rpx;
      background: #ff4d4f;
      color: #fff;
      font-size: 16rpx;
      border-radius: 12rpx;
      padding: 0 8rpx;
      min-width: 24rpx;
      text-align: center;
    }
    .circle {
      width: 90rpx;
      height: 90rpx;
      background: #1abc9c;
      border-radius: 50%;
      display: flex;
      align-items: center;
      justify-content: center;
      margin-bottom: 2rpx;
      margin-top: -30rpx;
      box-shadow: 0 2rpx 8rpx rgba(26,188,156,0.15);
    }
    .publish-btn {
      margin-top: -30rpx;
    }
  }
}
</style>
