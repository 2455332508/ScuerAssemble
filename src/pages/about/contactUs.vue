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

    <view class="info-section">
      <view class="info-item">
        <text class="label">商务邮箱：</text>
        <text class="value">{{ contactInfo.email }}</text>
      </view>
      <view class="info-item">
        <text class="label">微信号：</text>
        <text class="value">{{ contactInfo.wechat }}</text>
      </view>
      <view class="info-item">
        <text class="label">添加二维码</text>
      </view>
    </view>

    <view class="qrcode-section" v-if="contactInfo.qrcode">
      <image :src="contactInfo.qrcode" mode="widthFix" class="qrcode-img" />
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      contactInfo: {
        email: '',
        wechat: '',
        qrcode: ''
      }
    }
  },
  onLoad() {
    this.fetchContactInfo()
  },
  methods: {
    goBack() {
        uni.redirectTo({
      url: '/pages/about/aboutUs'
    })
    },
    fetchContactInfo() {
      uni.request({
        url: 'http://localhost:8080/about/concatUs',
        method: 'GET',
        success: (res) => {
          if (res.data && res.data.code === 200) {
            this.contactInfo = {
              email: res.data.data.email || '',
              wechat: res.data.data.wechat || '',
              qrcode: res.data.data.qrcode || ''
            }
          } else {
            uni.showToast({ title: '获取信息失败', icon: 'none' })
          }
        },
        fail: () => {
          uni.showToast({ title: '网络错误', icon: 'none' })
        }
      })
    }
  }
}
</script>

<style scoped lang="scss">
.contact-container {
  min-height: 100vh;
  background: #fff;
  padding-bottom: 40rpx;
}

.nav-bar {
  display: flex;
  align-items: center;
  height: 88rpx;
  background: #f8f8f8;
  border-bottom: 1px solid #eee;
  padding: 0 30rpx;
  position: relative;
}
.back-btn {
  display: flex;
  align-items: center;
  cursor: pointer;
}
.title {
  flex: 1;
  text-align: center;
  font-size: 32rpx;
  font-weight: 600;
  color: #222;
  margin-left: -40rpx;
}

.info-section {
  margin: 40rpx 30rpx 0 30rpx;
  background: #fafbfc;
  border-radius: 16rpx;
  box-shadow: 0 2rpx 8rpx rgba(0,0,0,0.03);
  padding: 30rpx 24rpx;
}
.info-item {
  display: flex;
  align-items: center;
  margin-bottom: 24rpx;
  font-size: 30rpx;
}
.label {
  color: #888;
  min-width: 140rpx;
}
.value {
  color: #222;
  font-weight: 500;
}

.qrcode-section {
  display: flex;
  flex-direction: column;
  align-items: center;
  margin-top: 60rpx;
}
.qrcode-img {
  width: 380rpx;
  height: 380rpx;
  background: #f5f5f5;
  border-radius: 16rpx;
  box-shadow: 0 2rpx 8rpx rgba(0,0,0,0.06);
}
</style>
