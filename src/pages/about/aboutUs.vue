<template>
  <view class="about-container">
    <!-- 顶部状态栏 -->
    <view class="status-bar">
      <view class="left">
        <text class="page-title">关于我们</text>
      </view>
      <view class="right">
        <text class="time">{{ currentTime }}</text>
      </view>
    </view>

    <!-- 主要内容区域 -->
    <view class="main-content">
      <!-- 轮播图部分 -->
      <swiper class="banner" 
        circular 
        autoplay 
        interval="3000" 
        duration="500"
        indicator-dots
        indicator-active-color="#2196F3">
        <swiper-item v-for="(item, index) in bannerList" :key="index">
          <image :src="item.imageUrl" mode="aspectFill" class="banner-image"></image>
        </swiper-item>
      </swiper>

      <!-- 功能按钮区域 -->
      <view class="button-container">
        <view class="feature-button" @click="onSCUFeatureClick">
          <uni-icons type="image" size="40" color="#2196F3"></uni-icons>
          <text class="button-text">川大风采</text>
        </view>
        <view class="feature-button" @click="onContactClick">
          <uni-icons type="phone" size="40" color="#2196F3"></uni-icons>
          <text class="button-text">联系我们</text>
        </view>
        <view class="feature-button" @click="onSpecificationClick">
          <uni-icons type="file" size="40" color="#2196F3"></uni-icons>
          <text class="button-text">使用规范</text>
        </view>
        <view class="feature-button" @click="onPrivacyClick">
          <uni-icons type="shield" size="40" color="#2196F3"></uni-icons>
          <text class="button-text">隐私协议</text>
        </view>
      </view>

      <!-- 文本内容弹窗 -->
      <uni-popup ref="textPopup" type="center">
        <view class="popup-content">
          <view class="popup-title">{{ popupTitle }}</view>
          <scroll-view class="popup-text" scroll-y>
            <text>{{ popupText }}</text>
          </scroll-view>
          <view class="popup-close" @click="closePopup">
            <uni-icons type="close" size="24" color="#666"></uni-icons>
          </view>
        </view>
      </uni-popup>
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
        <view class="tab-icon">
          <uni-icons :type="tab.icon" size="24" :color="currentTab === tab.key ? '#2196F3' : '#999'"></uni-icons>
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
      currentTime: '',
      timer: null,
      currentTab: 'about',
      popupTitle: '',
      popupText: '',
      bannerList: [
        { imageUrl: '../../static/banner1.jpg' },
        { imageUrl: '../../static/banner2.jpg' },
        { imageUrl: '../../static/banner3.jpg' }
      ],
      tabs: [
        { key: 'back', name: '返回上一级', icon: 'home' },
        { key: 'login', name: '返回登录界面', icon: 'person' },
        { key: 'navigation', name: '校内导航', icon: 'location' },
        { key: 'about', name: '关于我们', icon: 'info' }
      ]
    }
  },
  onLoad() {
    this.updateTime()
    this.timer = setInterval(this.updateTime, 1000)
  },
  onUnload() {
    if (this.timer) {
      clearInterval(this.timer)
    }
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
        case 'back':
          uni.navigateBack({
            delta: 1,
            fail: () => {
              uni.showToast({
                title: '已经是第一页了',
                icon: 'none'
              })
            }
          })
          break
          
        case 'login':
          uni.redirectTo({
            url: '/pages/login/index'
          })
          break
          
        case 'navigation':
          uni.showToast({
            title: '校内导航功能开发中',
            icon: 'none'
          })
          break
      }
    },
    onSCUFeatureClick() {
      uni.showToast({
        title: '川大风采功能开发中',
        icon: 'none'
      })
    },
    onContactClick() {
      uni.showToast({
        title: '联系我们功能开发中',
        icon: 'none'
      })
    },
    async onSpecificationClick() {
      try {
        uni.redirectTo({
            url: '/pages/about/specification'
          })
        
      } catch (error) {
        uni.showToast({
          title: '获取使用规范失败',
          icon: 'none'
        });
      }
    },
    
    async onPrivacyClick() {
      try {
        const response = await uni.request({
          url: '/static/privacy.txt',
          method: 'GET'
        });
        
        if (response[1].statusCode === 200) {
          this.popupTitle = '隐私协议';
          this.popupText = response[1].data;
          this.$refs.textPopup.open();
        } else {
          uni.showToast({
            title: '获取隐私协议失败',
            icon: 'none'
          });
        }
      } catch (error) {
        uni.showToast({
          title: '获取隐私协议失败',
          icon: 'none'
        });
      }
    },
    
    closePopup() {
      this.$refs.textPopup.close();
    }
  }
}
</script>

<style lang="scss">
.about-container {
  min-height: 100vh;
  background: linear-gradient(to bottom, #f8f9fa, #e9ecef);
  
  .main-content {
    padding-bottom: 100rpx;
  }
  
  .status-bar {
    position: fixed;
    top: 0;
    left: 0;
    right: 0;
    z-index: 100;
    height: 88rpx;
    background: rgba(255, 255, 255, 0.98);
    backdrop-filter: blur(20px);
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 0 30rpx;
    box-shadow: 0 4rpx 20rpx rgba(0, 0, 0, 0.05);
    
    .page-title {
      font-size: 32rpx;
      color: #333;
      font-weight: 600;
      background: linear-gradient(90deg, #2196F3, #64B5F6);
      -webkit-background-clip: text;
      background-clip: text;
      color: transparent;
    }
    
    .time {
      font-size: 28rpx;
      color: #333;
      font-weight: 500;
      letter-spacing: 2rpx;
      font-family: monospace;
    }
  }
  
  .banner {
    margin-top: 88rpx;
    height: 300rpx;
    box-shadow: 0 4rpx 20rpx rgba(0, 0, 0, 0.1);
    border-radius: 0 0 30rpx 30rpx;
    overflow: hidden;
    
    .banner-image {
      width: 100%;
      height: 100%;
      transition: transform 0.3s ease;
      
      &:hover {
        transform: scale(1.05);
      }
    }
  }

  .button-container {
    margin-top: 40rpx;
    padding: 0 40rpx;
    display: flex;
    flex-direction: column;
    gap: 30rpx;

    .feature-button {
      background: rgba(255, 255, 255, 0.9);
      padding: 40rpx;
      border-radius: 20rpx;
      display: flex;
      align-items: center;
      gap: 20rpx;
      box-shadow: 0 4rpx 20rpx rgba(0, 0, 0, 0.05);
      transition: all 0.3s ease;

      &:active {
        transform: scale(0.98);
        box-shadow: 0 2rpx 10rpx rgba(0, 0, 0, 0.05);
      }

      .button-text {
        font-size: 32rpx;
        color: #333;
        font-weight: 500;
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
    
    .tab-item {
      display: flex;
      flex-direction: column;
      align-items: center;
      padding: 10rpx 0;
      transition: all 0.3s ease;
      
      .tab-icon {
        margin-bottom: 4rpx;
        transition: transform 0.3s ease;
      }
      
      .tab-text {
        font-size: 24rpx;
        color: #999;
        transition: color 0.3s ease;
      }
      
      &.active {
        .tab-icon {
          transform: translateY(-4rpx);
        }
        
        .tab-text {
          color: #2196F3;
          font-weight: 500;
        }
      }
      
      &:active {
        transform: scale(0.9);
      }
    }
  }

  .popup-content {
    background: #fff;
    width: 80vw;
    max-height: 80vh;
    border-radius: 20rpx;
    padding: 40rpx;
    position: relative;
    
    .popup-title {
      font-size: 36rpx;
      font-weight: 600;
      color: #333;
      margin-bottom: 30rpx;
      text-align: center;
    }
    
    .popup-text {
      height: 60vh;
      font-size: 28rpx;
      color: #666;
      line-height: 1.6;
    }
    
    .popup-close {
      position: absolute;
      top: 20rpx;
      right: 20rpx;
      padding: 10rpx;
      
      &:active {
        opacity: 0.7;
      }
    }
  }
}
</style>
