<template>
  <view class="login-container">
    <!-- 顶部状态栏 -->
    <view class="status-bar">
      <text>12:00</text>
    </view>
    
    <!-- 用户头像区域 -->
    <view class="avatar-section">
      <image class="default-avatar" src="..\..\static\default-avatar.jpg" mode="aspectFit"></image>
    </view>
    
    <!-- 登录表单区域 -->
    <view class="login-form">
      <view class="input-group">
        <input class="input-item" type="text" v-model="account" placeholder="注册账号" />
        <view class="input-line"></view>
      </view>
      
      <view class="input-group">
        <input class="input-item" type="password" v-model="password" placeholder="密码" />
        <view class="input-line"></view>
      </view>
      
      <!-- 登录按钮区域 -->
      <view class="button-group">
        <button class="login-btn student" @click="handleStudentLogin">
          <text class="btn-text">学生登录</text>
        </button>
        <button class="login-btn admin" @click="handleAdminLogin">
          <text class="btn-text">管理员登录</text>
        </button>
        <button class="login-btn visitor" @click="handleVisitorAccess">
          <text class="btn-text">游客访问</text>
        </button>
      </view>
      
      <!-- 注册链接 -->
      <view class="register-link">
        <text @click="goToRegister" class="register-text">注册账号</text>
      </view>
      
      <!-- 用户协议 -->
      <view class="agreement">
        <checkbox :checked="agreed" @tap="agreed = !agreed" class="custom-checkbox" />
        <text>我已阅读并同意</text>
        <text class="link">《用户协议》</text>
        <text>和</text>
        <text class="link">《隐私政策》</text>
      </view>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      account: '',
      password: '',
      agreed: false
    }
  },
  methods: {
    handleStudentLogin() {
      if (!this.validateForm()) return
      // 处理学生登录逻辑
      console.log(this.account, this.password)
      uni.request({
        url: 'http://localhost:8080/student/login',
        method: 'GET',
        data: {
          account: this.account,
          password: this.password
        },
        success: (res) => {
          if (res.data.code == 200) {
            uni.showToast({
              title: '登录成功',
              icon: 'success'
            })
            // 登录成功后跳转到主页面
            uni.navigateTo({
              url: '../home/studentIndex'
            })
          } else {
            uni.showToast({
              title: '账号或密码错误',
              icon: 'none'
            })
          }
        },
        fail: () => {
          uni.showToast({
            title: '网络错误，请稍后重试',
            icon: 'none'
          })
        }
      })
    },
    handleAdminLogin() {
      uni.navigateTo({
        url: '../admin/mainPage1'
      })
      /* 
      if (!this.validateForm()) return
      console.log(this.account, this.password)
      // 处理管理员登录逻辑
      uni.request({
        url: 'http://localhost:8080/admin/login',
        method: 'GET',
        data: {
          account: this.account,
          password: this.password
        },
        success: (res) => {
          if (res.data.code == 200) {
            uni.showToast({
              title: '登录成功',
              icon: 'success'
            })
            // 登录成功后跳转到主页面
            uni.navigateTo({
              url: '../home/adminIndex'
            })
          } else {
            uni.showToast({
              title: '账号或密码错误',
              icon: 'none'
            })
          }
        },
        fail: () => {
          uni.showToast({
            title: '网络错误，请稍后重试',
            icon: 'none'
          })
        }
      })
      */
    },
    handleVisitorAccess() {
      // 直接跳转到主页面
      uni.navigateTo({
        url: '../home/visitorIndex'
      })
    },
    goToRegister() {
      uni.navigateTo({
        url: '../register/index'
      })
    },
    validateForm() {
      if (!this.account || !this.password) {
        uni.showToast({
          title: '请输入账号和密码',
          icon: 'none'
        })
        return false
      }
      if (!this.agreed) {
        uni.showToast({
          title: '请同意用户协议和隐私政策',
          icon: 'none'
        })
        return false
      }
      return true
    }
  }
}
</script>

<style lang="scss">
.login-container {
  min-height: 100vh;
  background: linear-gradient(135deg, #f5f7fa 0%, #e4e8f0 100%);
  
  .status-bar {
    padding: 20rpx;
    text-align: center;
    color: #333;
    font-size: 28rpx;
    opacity: 0.8;
  }
  
  .avatar-section {
    padding: 80rpx 0;
    display: flex;
    justify-content: center;
    
    .default-avatar {
      width: 180rpx;
      height: 180rpx;
      border-radius: 90rpx;
      background-color: #fff;
      box-shadow: 0 8rpx 16rpx rgba(0, 0, 0, 0.1);
      transition: transform 0.3s ease;
      
      &:active {
        transform: scale(0.95);
      }
    }
  }
  
  .login-form {
    padding: 0 60rpx;
    
    .input-group {
      position: relative;
      margin-bottom: 40rpx;
      
      .input-item {
        width: 90%;
        height: 100rpx;
        background: rgba(255, 255, 255, 0.9);
        border: none;
        border-radius: 50rpx;
        padding: 0 40rpx;
        font-size: 30rpx;
        color: #333;
        box-shadow: 0 4rpx 12rpx rgba(0, 0, 0, 0.05);
        transition: all 0.3s ease;
        
        &:focus {
          box-shadow: 0 6rpx 16rpx rgba(0, 0, 0, 0.1);
          background: #93919162;
        }
      }
      
      .input-line {
        position: absolute;
        bottom: 0;
        left: 50%;
        transform: translateX(-50%);
        width: 0;
        height: 3rpx;
        background: #2196F3;
        transition: width 0.3s ease;
      }
      
      &:focus-within .input-line {
        width: 90%;
      }
    }
    
    .button-group {
      margin-top: 60rpx;
      

      .login-btn {
        width: 100%;
        height: 100rpx;
        border-radius: 50rpx;
        margin-bottom: 30rpx;
        border: none;
        position: relative;
        overflow: hidden;
        transition: all 0.05s ease;
        transform: scale(1); // 初始化缩放比例为1
        background: linear-gradient(135deg, #2196F3 0%, #1976D2 100%);
        box-shadow: 0 8rpx 16rpx rgba(33, 149, 243, 0.701);
        cursor: pointer;

        

        .btn-text {
          font-size: 32rpx;
          font-weight: 500;
          color: #fff;
          position: relative;
          z-index: 1;
        }
        
        &.student {
          background: linear-gradient(135deg, #00C853 0%, #009624 100%);
          box-shadow: 0 8rpx 16rpx rgba(0, 200, 83, 0.3);
          &:active {
          transform: scale(0.9); // 按下时缩小到0.9倍
          background: linear-gradient(135deg, #00C853 0%, #00962337 100%); // 调整背景颜色加深
          box-shadow: 0 4rpx 8rpx rgba(0, 200, 83, 0.3); // 调整阴影效果
        }
        }
        
        &.admin {
          background: linear-gradient(135deg, #2196F3 0%, #1976D2 100%);
          box-shadow: 0 8rpx 16rpx rgba(33, 150, 243, 0.3);
          &:active {
          transform: scale(0.9); // 按下时缩小到0.9倍
          background: linear-gradient(135deg, #1976D2 0%, #0c5cb841 100%); // 调整背景颜色加深
          box-shadow: 0 4rpx 8rpx rgba(33, 150, 243, 0.3); // 调整阴影效果
        }
        }
        
        &.visitor {
          background: linear-gradient(135deg, #90CAF9 0%, #64B5F6 100%);
          box-shadow: 0 8rpx 16rpx rgba(144, 202, 249, 0.3);
          &:active {
          transform: scale(0.9); // 按下时缩小到0.9倍
          background: linear-gradient(135deg, #90CAF9 0%, #64b4f641 100%); // 调整背景颜色加深
          box-shadow: 0 4rpx 8rpx rgba(144, 202, 249, 0.3); // 调整阴影效果
        }
        }
      }
    }
    
    .register-link {
      text-align: center;
      margin: 40rpx 0;
      
      .register-text {
        font-size: 28rpx;
        color: #2196F3;
        display: inline-block;
        padding: 10rpx 30rpx;
        border-radius: 30rpx;
        background: rgba(33, 150, 243, 0.1);
        transition: all 0.3s ease;
        
        &:active {
          background: rgba(33, 150, 243, 0.2);
          transform: scale(0.98);
        }
      }
    }
    
    .agreement {
      display: flex;
      align-items: center;
      justify-content: center;
      font-size: 24rpx;
      color: #666;
      margin-top: 60rpx;
      
      .custom-checkbox {
        transform: scale(0.8);
        margin-right: 10rpx;
      }
      
      .link {
        color: #2196F3;
        margin: 0 4rpx;
        position: relative;
        
        &::after {
          content: '';
          position: absolute;
          bottom: -2rpx;
          left: 0;
          width: 100%;
          height: 1rpx;
          background: #2196F3;
          transform: scaleX(0);
          transition: transform 0.3s ease;
        }
        
        &:active::after {
          transform: scaleX(1);
        }
      }
    }
  }
}
</style> 