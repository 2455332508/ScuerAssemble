<template>
  <view class="register-container">
    <!-- 顶部导航栏 -->
    <view class="nav-bar">
      <text>注册</text>
    </view>
    
    <!-- 标题 -->
    <view class="title">
      <text>川大集合吧</text>
    </view>
    
    <!-- 注册表单 -->
    <view class="register-form">
      <!-- 用户名输入 -->
      <view class="input-group" :class="{ 'error': errors.username }">
        <input 
          class="input-item" 
          type="text" 
          v-model="formData.username" 
          placeholder="请输入用户名"
          @input="validateUsername"
        />
        <text v-if="errors.username" class="error-text">{{ errors.username }}</text>
      </view>
      
      <!-- 学号输入 -->
      <view class="input-group" :class="{ 'error': errors.studentId }">
        <input 
          class="input-item" 
          type="text" 
          v-model="formData.studentId" 
          placeholder="请输入13位学号"
          @input="validateStudentId"
        />
        <text v-if="errors.studentId" class="error-text">{{ errors.studentId }}</text>
      </view>
      
      <!-- 手机号输入 -->
      <view class="input-group" :class="{ 'error': errors.phone }">
        <input 
          class="input-item" 
          type="number" 
          v-model="formData.phone" 
          placeholder="请输入手机号"
          @input="validatePhone"
        />
        <text v-if="errors.phone" class="error-text">{{ errors.phone }}</text>
      </view>
      
      <!-- 密码输入 -->
      <view class="input-group" :class="{ 'error': errors.password }">
        <input 
          class="input-item" 
          type="password" 
          v-model="formData.password" 
          placeholder="请输入密码"
          @input="validatePassword"
        />
        <text v-if="errors.password" class="error-text">{{ errors.password }}</text>
      </view>
      
      <!-- 确认密码输入 -->
      <view class="input-group" :class="{ 'error': errors.confirmPassword }">
        <input 
          class="input-item" 
          type="password" 
          v-model="formData.confirmPassword" 
          placeholder="请确认密码"
          @input="validateConfirmPassword"
        />
        <text v-if="errors.confirmPassword" class="error-text">{{ errors.confirmPassword }}</text>
      </view>
      
      <!-- 下一步按钮 -->
      <button class="next-btn" :disabled="!isFormValid" @click="handleNext">下一步</button>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      formData: {
        username: '',
        studentId: '',
        phone: '',
        password: '',
        confirmPassword: ''
      },
      errors: {
        username: '',
        studentId: '',
        phone: '',
        password: '',
        confirmPassword: ''
      }
    }
  },
  computed: {
    isFormValid() {
      return !Object.values(this.errors).some(error => error) && 
             Object.values(this.formData).every(value => value)
    }
  },
  methods: {
    validateUsername() {
      if (!this.formData.username) {
        this.errors.username = '请输入用户名'
      } else if (this.formData.username.length < 2) {
        this.errors.username = '用户名至少2个字符'
      } else {
        this.errors.username = ''
      }
    },
    validateStudentId() {
      if (!this.formData.studentId) {
        this.errors.studentId = '请输入学号'
      } else if (!/^\d{13}$/.test(this.formData.studentId)) {
        this.errors.studentId = '请输入13位数字学号'
      } else {
        this.errors.studentId = ''
      }
    },
    validatePhone() {
      if (!this.formData.phone) {
        this.errors.phone = '请输入手机号'
      } else if (!/^1[3-9]\d{9}$/.test(this.formData.phone)) {
        this.errors.phone = '请输入正确的手机号'
      } else {
        this.errors.phone = ''
      }
    },
    validatePassword() {
      if (!this.formData.password) {
        this.errors.password = '请输入密码'
      } else if (this.formData.password.length < 6) {
        this.errors.password = '密码至少6个字符'
      } else {
        this.errors.password = ''
      }
      if (this.formData.confirmPassword) {
        this.validateConfirmPassword()
      }
    },
    validateConfirmPassword() {
      if (!this.formData.confirmPassword) {
        this.errors.confirmPassword = '请确认密码'
      } else if (this.formData.confirmPassword !== this.formData.password) {
        this.errors.confirmPassword = '两次输入的密码不一致'
      } else {
        this.errors.confirmPassword = ''
      }
    },
    handleNext() {
      if (!this.isFormValid) {
        uni.showToast({
          title: '请完善所有信息',
          icon: 'none'
        })
        return
      }
      
      // 将表单数据转换为URL参数
      const registerData = encodeURIComponent(JSON.stringify({
        username: this.formData.username,
        studentId: this.formData.studentId,
        phone: this.formData.phone,
        password: this.formData.password
      }))
      
      uni.navigateTo({
        url: `./info?data=${registerData}`
      })
    }
  }
}
</script>

<style lang="scss">
.register-container {
  min-height: 100vh;
  background: linear-gradient(135deg, #f5f7fa 0%, #e4e8f0 100%);
  padding: 0 60rpx;
  
  .nav-bar {
    height: 88rpx;
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: 32rpx;
    font-weight: bold;
    color: #333;
  }
  
  .title {
    margin: 80rpx 0;
    text-align: center;
    font-size: 44rpx;
    font-weight: bold;
    color: #333;
    text-shadow: 0 2rpx 4rpx rgba(0, 0, 0, 0.1);
  }
  
  .register-form {
    .input-group {
      margin-bottom: 40rpx;
      position: relative;
      
      &.error {
        .input-item {
          border-color: #ff4d4f;
          box-shadow: 0 4rpx 12rpx rgba(255, 77, 79, 0.1);
        }
      }
    }
    
    .input-item {
      width: 90%;
      height: 100rpx;
      background: rgba(255, 255, 255, 0.9);
      border: 1px solid #eee;
      border-radius: 50rpx;
      padding: 0 40rpx;
      font-size: 30rpx;
      color: #333;
      box-shadow: 0 4rpx 12rpx rgba(0, 0, 0, 0.05);
      transition: all 0.3s ease;
      
      &:focus {
        border-color: #2196F3;
        box-shadow: 0 6rpx 16rpx rgba(33, 150, 243, 0.1);
        background: #fff;
      }
    }
    
    .error-text {
      font-size: 24rpx;
      color: #ff4d4f;
      margin-top: 8rpx;
      padding-left: 40rpx;
      animation: shake 0.5s ease;
    }
    
    .next-btn {
      width: 100%;
      height: 100rpx;
      background: linear-gradient(135deg, #2196F3 0%, #1976D2 100%);
      color: #fff;
      border-radius: 50rpx;
      margin-top: 80rpx;
      font-size: 32rpx;
      font-weight: 500;
      display: flex;
      align-items: center;
      justify-content: center;
      border: none;
      box-shadow: 0 8rpx 16rpx rgba(33, 150, 243, 0.3);
      transition: all 0.3s ease;
      
      &:active {
        transform: scale(0.98);
        box-shadow: 0 4rpx 8rpx rgba(33, 150, 243, 0.3);
      }
      
      &:disabled {
        background: #ccc;
        box-shadow: none;
      }
    }
  }
}

@keyframes shake {
  0%, 100% { transform: translateX(0); }
  25% { transform: translateX(-5rpx); }
  75% { transform: translateX(5rpx); }
}
</style>
