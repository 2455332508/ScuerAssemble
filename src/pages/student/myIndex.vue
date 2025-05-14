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

    <!-- 底部导航栏 -->
    <view class="tab-bar">
      <view 
        class="tab-item" 
        v-for="(tab, index) in tabs" 
        :key="index"
        :class="{ active: currentTab === tab.key }"
        @click="switchTab(tab.key)"
      >
        <view class="tab-icon-bg" :class="{ 'tab-publish': tab.key === 'publish' }">
          <uni-icons :type="tab.icon" :size="tab.key === 'publish' ? 36 : 26" :color="currentTab === tab.key ? (tab.key === 'publish' ? '#fff' : '#2196F3') : (tab.key === 'publish' ? '#2196F3' : '#999')"></uni-icons>
        </view>
        <text class="tab-text">{{ tab.name }}</text>
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
          { text: '意见反馈', icon: 'chatbubble' },
          { text: '联系我们', icon: 'phone' },
          { text: '使用规范', icon: 'file' },
          { text: '隐私协议', icon: 'shield' },
          { text: '登出', icon: 'log-out' }
        ],
        tabs: [
          { key: 'home', name: '首页', icon: 'home' },
          { key: 'circle', name: '围合', icon: 'chatboxes' },
          { key: 'publish', name: '发布', icon: 'plusempty' },
          { key: 'msg', name: '消息', icon: 'email' },
          { key: 'mine', name: '我的', icon: 'person' }
        ],
        currentTab: 'mine'
      }
    },
    methods: {
      onMenuClick(item) {
        switch (item.text) {
          case '我的帖子':
            uni.navigateTo ({
              url: '/pages/post/myposts'
            })
            break
          case '个人信息':
            uni.navigateTo ({
              url: '/pages/student/profile'
            })
            break
          case '地图导航':
            uni.navigateTo ({
              url: '/pages/map/Map'
            })
            break
          case '意见反馈':
            uni.navigateTo ({
              url: '/pages/feedback/index'
            })
            break
          case '联系我们':
            try {
              uni.navigateTo ({
                url: '/pages/about/contactUs'
              })
            } catch (error) {
              uni.showToast({
                title: '联系我们功能开发中',
                icon: 'none'
              })
            }
            break
          case '使用规范':
            try {
              uni.navigateTo ({
                url: '/pages/about/specification'
              })
            } catch (error) {
              uni.showToast({
                title: '获取使用规范失败',
                icon: 'none'
              })
            }
            break
          case '隐私协议':
            try {
              uni.navigateTo ({
                url: '/pages/about/privacy'
              })
            } catch (error) {
              uni.showToast({
                title: '获取隐私协议失败',
                icon: 'none'
              })
            }
            break
          case '登出':
            uni.redirectTo({ url: '/pages/login/index' })
            break
          default:
            uni.showToast({
              title: '功能开发中',
              icon: 'none'
            })
        }
      },
      switchTab(key) {
        if (this.currentTab === key) return
        this.currentTab = key
        switch (key) {
          case 'home':
            uni.navigateTo({ url: '/pages/student/studentIndex' })
            break
          case 'circle':
            uni.navigateTo({ url: '/pages/student/dorm/dormIndex' })
            break
          case 'publish':
            uni.navigateTo({ url: '/pages/student/publish' })
            break
          case 'msg':
            uni.navigateTo({ url: '/pages/message/message' })
            break
          case 'mine':
            // 当前页
            break
        }
      }
    }
  }
  </script>
  
  <style scoped lang="scss">
  .my-container { background: #fff; min-height: 100vh; padding-bottom: 100rpx; }
  .header { display: flex; align-items: center; padding: 40rpx 30rpx 20rpx; background: #f7f7f7; border-bottom: 1px solid #eee; }
  .avatar { width: 100rpx; height: 100rpx; border-radius: 50%; margin-right: 20rpx; }
  .user-info { flex: 1; }
  .username { font-size: 32rpx; font-weight: bold; }
  .identity { display: block; font-size: 24rpx; color: #888; margin-top: 8rpx; }
  .setting-icon { color: #888; }
  .menu-list { margin-top: 30rpx; }
  .menu-item { display: flex; align-items: center; padding: 30rpx; border-bottom: 1px solid #f0f0f0; }
  .menu-text { flex: 1; margin-left: 20rpx; font-size: 30rpx; }

  .tab-bar {
    position: fixed;
    bottom: 0;
    left: 0;
    right: 0;
    height: 100rpx;
    background: rgba(255, 255, 255, 0.95);
    backdrop-filter: blur(20px);
    display: flex;
    justify-content: space-around;
    align-items: center;
    box-shadow: 0 -4rpx 20rpx rgba(0, 0, 0, 0.05);
    border-radius: 30rpx 30rpx 0 0;
    padding-bottom: env(safe-area-inset-bottom);
    z-index: 99;
  }
  .tab-item {
    display: flex;
    flex-direction: column;
    align-items: center;
    padding: 10rpx 0;
    transition: all 0.3s ease;
    flex: 1;
  }
  .tab-icon-bg {
    width: 44px;
    height: 44px;
    display: flex;
    align-items: center;
    justify-content: center;
    border-radius: 50%;
    margin-bottom: 4px;
  }
  .tab-publish {
    background: linear-gradient(90deg, #2196F3, #64B5F6);
    box-shadow: 0 2px 8px #2196f355;
  }
  .tab-text {
    font-size: 24rpx;
    color: #999;
    transition: color 0.3s ease;
  }
  .tab-item.active .tab-text {
    color: #2196F3;
    font-weight: 500;
  }


  </style>