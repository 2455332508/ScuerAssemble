<template>
  <view class="visitor-container">
    <!-- 顶部状态栏（复用studentIndex.vue） -->
    <view class="status-bar">
      <view class="left">
        <image class="user-avatar" :src="userInfo.photo || '../../../static/default-avatar.jpg'" mode="aspectFill"></image>
      </view>
      <view class="right">
        <text class="time">{{ currentTime }}</text>
      </view>
    </view>

    <!-- 主要内容区域 -->
    <view class="main-content">
      <!-- 我的围合（单个，靠左，区域高度较小） -->
      <view class="section-title">我的围合</view>
      <view class="my-dorm-single">
        <image :src="myDorm.icon" class="dorm-icon" />
        <text class="dorm-name">{{ myDorm.name }}</text>
      </view>

      <!-- 其他围合（双列排布） -->
      <view class="section-title">其他围合</view>
      <view class="other-dorms-grid">
        <view class="other-dorm-item" v-for="dorm in otherDorms" :key="dorm.id">
          <image :src="dorm.icon" class="dorm-icon" />
          <view class="dorm-info">
            <text class="dorm-name">{{ dorm.name }}</text>
            <text class="dorm-hot">热度 {{ dorm.hot }}</text>
          </view>
        </view>
      </view>
    </view>

    <!-- 底部导航栏（复用studentIndex.vue） -->
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
  </view>
</template>

<script>
export default {
  data() {
    return {
      token: '',
      currentTime: '',
      userInfo: {
        photo: ''
      },
      myDorm: { id: 1, name: '四川大学', icon: '../../static/dorm-scu.png' },
      otherDorms: [
        { id: 2, name: '绝区零', icon: '../../static/dorm-juequling.png', hot: '10.4W' },
        { id: 3, name: '抗压背锅', icon: '../../static/dorm-kybg.png', hot: '235.3W' },
        { id: 4, name: 'steam', icon: '../../static/dorm-steam.png', hot: '42.7W' },
        { id: 5, name: 'lol', icon: '../../static/dorm-lol.png', hot: '6.6W' },
        { id: 6, name: '崩坏星穹铁道', icon: '../../static/dorm-bh.png', hot: '21.7W' },
        { id: 7, name: '文明6', icon: '../../static/dorm-civ6.png', hot: '5.8W' },
        { id: 8, name: 'csgo', icon: '../../static/dorm-csgo.png', hot: '62.5W' },
        { id: 9, name: '图拉丁', icon: '../../static/dorm-tld.png', hot: '62.7W' },
        { id: 10, name: '电子科技大学', icon: '../../static/dorm-uestc.png', hot: '1.6W' },
        { id: 11, name: '四川大学', icon: '../../static/dorm-scu.png', hot: '3W' },
        { id: 12, name: '罪恶装备', icon: '../../static/dorm-gear.png', hot: '8.9K' }
      ],
      tabs: [
        { key: 'home', name: '首页', icon: 'home' },
        { key: 'circle', name: '围合', icon: 'chatboxes' },
        { key: 'publish', name: '发布', icon: 'plusempty' },
        { key: 'msg', name: '消息', icon: 'email' },
        { key: 'mine', name: '我的', icon: 'person' }
      ],
      currentTab: 'circle'
    }
  },
  onLoad() {
    // 获取本地存储的token
    this.token = uni.getStorageSync('token')
    if (!this.token) {
      uni.showToast({
        title: '请先登录',
        icon: 'none'
      })
      // setTimeout(() => {
      //   uni.reLaunch({
      //     url: '/pages/login/index'
      //   })
      // }, 1500)
      // return
    }

    this.updateTime()
    this.timer = setInterval(this.updateTime, 1000)
    // 获取我的围合
    uni.request({
      url: 'http://localhost:8080/student/getDorm',
      method: 'GET',
      header: {
        'Authorization': this.token
      },
      success: (res) => {
        if (res.data.code === 200) {
          if (res.data.msg === 'NOT_LOGIN') {
            uni.showToast({
              title: '登录失效，请重新登录',
              icon: 'none'
            })
            // setTimeout(() => {
            //   uni.reLaunch({
            //     url: '/pages/login/index'
            //   })
            // }, 1500)
            // return
          }
          if (res.data.data) {
            this.myDorm = res.data.data
          }
        }
      },
      fail: () => {
        // 保留本地测试数据
      }
    })
    // 获取其他围合
    uni.request({
      url: 'http://localhost:8080/dorm/list',
      method: 'GET',
      header: {
        'Authorization': this.token
      },
      success: (res) => {
        if (res.data.code === 200) {
          if (res.data.msg === 'NOT_LOGIN') {
            uni.showToast({
              title: '登录失效，请重新登录',
              icon: 'none'
            })
            // setTimeout(() => {
            //   uni.reLaunch({
            //     url: '/pages/login/index'
            //   })
            // }, 1500)
            // return
          }
          if (Array.isArray(res.data.data)) {
            this.otherDorms = res.data.data
          }
        }
      },
      fail: () => {
        // 保留本地测试数据
      }
    })
  },
  onUnload() {
    if (this.timer) clearInterval(this.timer)
  },
  methods: {
    updateTime() {
      const now = new Date()
      const year = now.getFullYear()
      const month = (now.getMonth() + 1).toString().padStart(2, '0')
      const day = now.getDate().toString().padStart(2, '0')
      const weekdays = ['星期日', '星期一', '星期二', '星期三', '星期四', '星期五', '星期六']
      const weekday = weekdays[now.getDay()]
      this.currentTime = `${year}年${month}月${day}日 ${weekday}`
    },
    switchTab(key) {
      if (this.currentTab === key) return
      this.currentTab = key
      switch (key) {
        case 'home':
          uni.redirectTo({ url: '/pages/student/studentIndex' })
          break
        case 'circle':
          // 当前页
          break
        case 'publish':
          uni.navigateTo({ url: '/pages/student/publish' })
          break
        case 'msg':
          uni.redirectTo({ url: '/pages/message/message' })
          break
        case 'mine':
          uni.redirectTo({ url: '/pages/student/myIndex' })
          break
      }
    }
  }
}
</script>

<style scoped lang="scss">
.visitor-container {
  background: #fff;
  min-height: 100vh;
  padding-bottom: 100rpx;
}
.status-bar {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 30rpx 30rpx 0 30rpx;
  .user-avatar {
    width: 60rpx;
    height: 60rpx;
    border-radius: 50%;
  }
  .time {
    color: #888;
    font-size: 28rpx;
  }
}
.main-content {
  padding: 20rpx 0 0 0;
}
.section-title {
  font-size: 32rpx;
  font-weight: bold;
  color: #222;
  margin: 30rpx 30rpx 10rpx 30rpx;
}
.my-dorm-single {
  display: flex;
  align-items: center;
  margin: 0 30rpx 10rpx 30rpx;
  height: 80rpx;
  .dorm-icon {
    width: 60rpx;
    height: 60rpx;
    border-radius: 16rpx;
    margin-right: 16rpx;
    background: #f5f5f5;
  }
  .dorm-name {
    font-size: 28rpx;
    color: #222;
    font-weight: 500;
  }
}
.other-dorms-grid {
  display: flex;
  flex-wrap: wrap;
  margin: 0 10rpx;
  .other-dorm-item {
    width: 48%;
    background: #fafbfc;
    border-radius: 16rpx;
    display: flex;
    align-items: center;
    margin: 1% 1% 18rpx 1%;
    box-sizing: border-box;
    padding: 16rpx 12rpx;
    box-shadow: 0 2rpx 8rpx #eee;
    .dorm-icon {
      width: 48rpx;
      height: 48rpx;
      border-radius: 12rpx;
      margin-right: 12rpx;
      background: #f5f5f5;
    }
    .dorm-info {
      display: flex;
      flex-direction: column;
      .dorm-name {
        font-size: 26rpx;
        color: #222;
        font-weight: 500;
      }
      .dorm-hot {
        font-size: 22rpx;
        color: #888;
        margin-top: 4rpx;
      }
    }
  }
}
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
