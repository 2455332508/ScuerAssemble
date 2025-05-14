<template>
  <view class="contact-container">
    <!-- 顶部导航栏 -->
    <view class="nav-bar">
      <view class="back-btn" @click="goBack">
        <uni-icons type="back" size="24" color="#333" />
        <text>返回</text>
      </view>
      <text class="title">联系我们</text>
    </view>

    <view class="content-wrapper">
      <view class="header-section">
        <image class="logo" src="../../static/logo.png" mode="aspectFit" />
        <text class="welcome-text">欢迎联系我们</text>
        <text class="sub-text">我们将竭诚为您服务</text>
      </view>

      <view class="info-section">
        <view class="section-title">
          <uni-icons type="info" size="20" color="#2196F3" />
          <text>联系方式</text>
        </view>
        
        <view class="info-item">
          <view class="item-left">
            <uni-icons type="email" size="24" color="#2196F3" />
            <text class="label">商务邮箱</text>
          </view>
          <text class="value">{{ contactInfo.email }}</text>
        </view>

        <view class="info-item">
          <view class="item-left">
            <uni-icons type="weixin" size="24" color="#2196F3" />
            <text class="label">微信号</text>
          </view>
          <text class="value">{{ contactInfo.wechat }}</text>
        </view>
      </view>

      <view class="qrcode-section">
        <view class="section-title">
          <uni-icons type="scan" size="20" color="#2196F3" />
          <text>扫码关注</text>
        </view>
        <view class="qrcode-wrapper">
          <image :src="contactInfo.qrcode || qrcode" mode="aspectFit" class="qrcode-img" />
          <text class="qrcode-tip">扫描上方二维码添加微信</text>
        </view>
      </view>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      contactInfo: {
        email: '2455332508@qq.com',
        wechat: '19181761096',
        qrcode: ''
      },
      qrcode: '../../static/default-avatar.jpg'
    }
  },
  onLoad() {
    // this.fetchContactInfo()
  },
  methods: {
    goBack() {
      //   uni.redirectTo({
      // url: '/pages/about/aboutUs'
      uni.navigateBack({
        delta: 1
    })
    },
    // fetchContactInfo() {
    //   uni.request({
    //     url: 'http://localhost:8080/about/concatUs',
    //     method: 'GET',
    //     success: (res) => {
    //       if (res.data && res.data.code === 200) {
    //         this.contactInfo = {
    //           email: res.data.data.email || '',
    //           wechat: res.data.data.wechat || '',
    //           qrcode: res.data.data.qrcode || ''
    //         }
    //       } else {
    //         uni.showToast({ title: '获取信息失败', icon: 'none' })
    //       }
    //     },
    //     fail: () => {
    //       uni.showToast({ title: '网络错误', icon: 'none' })
    //     }
    //   })
    // }
  }
}
</script>

<style scoped lang="scss">
.contact-container {
  min-height: 100vh;
  background: #f8f9fa;
}

.nav-bar {
  display: flex;
  align-items: center;
  height: 88rpx;
  background: #fff;
  border-bottom: 1px solid #eee;
  padding: 0 30rpx;
  position: relative;
  box-shadow: 0 2rpx 8rpx rgba(0,0,0,0.05);
}

.back-btn {
  display: flex;
  align-items: center;
  cursor: pointer;
  padding: 10rpx;
}

.title {
  flex: 1;
  text-align: center;
  font-size: 32rpx;
  font-weight: 600;
  color: #222;
  margin-left: -40rpx;
}

.content-wrapper {
  padding: 30rpx;
}

.header-section {
  display: flex;
  flex-direction: column;
  align-items: center;
  margin-bottom: 40rpx;
  
  .logo {
    width: 160rpx;
    height: 160rpx;
    margin-bottom: 20rpx;
  }
  
  .welcome-text {
    font-size: 36rpx;
    font-weight: 600;
    color: #333;
    margin-bottom: 10rpx;
  }
  
  .sub-text {
    font-size: 28rpx;
    color: #666;
  }
}

.section-title {
  display: flex;
  align-items: center;
  margin-bottom: 24rpx;
  
  text {
    font-size: 30rpx;
    font-weight: 600;
    color: #333;
    margin-left: 10rpx;
  }
}

.info-section {
  background: #fff;
  border-radius: 16rpx;
  padding: 30rpx;
  margin-bottom: 30rpx;
  box-shadow: 0 2rpx 12rpx rgba(0,0,0,0.05);
}

.info-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 20rpx 0;
  border-bottom: 1px solid #f5f5f5;
  
  &:last-child {
    border-bottom: none;
  }
  
  .item-left {
    display: flex;
    align-items: center;
    
    .label {
      font-size: 28rpx;
      color: #666;
      margin-left: 16rpx;
    }
  }
  
  .value {
    font-size: 28rpx;
    color: #333;
    font-weight: 500;
  }
}

.qrcode-section {
  background: #fff;
  border-radius: 16rpx;
  padding: 30rpx;
  box-shadow: 0 2rpx 12rpx rgba(0,0,0,0.05);
}

.qrcode-wrapper {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 20rpx 0;
  
  .qrcode-img {
    width: 400rpx;
    height: 400rpx;
    background: #f8f9fa;
    border-radius: 16rpx;
    padding: 20rpx;
    box-shadow: 0 2rpx 8rpx rgba(0,0,0,0.06);
  }
  
  .qrcode-tip {
    font-size: 26rpx;
    color: #666;
    margin-top: 20rpx;
  }
}
</style>
