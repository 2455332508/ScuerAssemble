<template>
    <view class="my-container">
      <view class="header">
        <image class="avatar" src="../../static/default-avatar.jpg"></image>
        <view class="user-info">
          <text class="username">用户名</text>
          <text class="identity">身份</text>
        </view>
        <uni-icons type="gear" size="24" class="setting-icon"></uni-icons>
      </view>
      <view class="menu-list">
        <view class="menu-item" v-for="item in menu" :key="item.text" @click="onMenuClick(item)">
          <uni-icons :type="item.icon" size="24"></uni-icons>
          <text class="menu-text">{{ item.text }}</text>
          <uni-icons type="arrowright" size="18"></uni-icons>
        </view>
      </view>
    </view>
  </template>
  
  <script>
  export default {
    data() {
      return {
        menu: [
          { text: '我的帖子', icon: 'wallet' },
          { text: '个人信息', icon: 'person' },
          { text: '地图导航', icon: 'location' },
          { text: '意见反馈', icon: 'chatbubble' }
        ]
      }
    },
    methods: {
      onMenuClick(item) {
        uni.showToast({
          title: '功能开发中',
          icon: 'none'
        })
      }
    }
  }
  </script>
  
  <style scoped>
  .my-container { background: #fff; min-height: 100vh; }
  .header { display: flex; align-items: center; padding: 40rpx 30rpx 20rpx; background: #f7f7f7; border-bottom: 1px solid #eee; }
  .avatar { width: 100rpx; height: 100rpx; border-radius: 50%; margin-right: 20rpx; }
  .user-info { flex: 1; }
  .username { font-size: 32rpx; font-weight: bold; }
  .identity { display: block; font-size: 24rpx; color: #888; margin-top: 8rpx; }
  .setting-icon { color: #888; }
  .menu-list { margin-top: 30rpx; }
  .menu-item { display: flex; align-items: center; padding: 30rpx; border-bottom: 1px solid #f0f0f0; }
  .menu-text { flex: 1; margin-left: 20rpx; font-size: 30rpx; }
  </style>