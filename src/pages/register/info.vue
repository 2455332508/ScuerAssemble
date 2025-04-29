<template>
  <view class="info-container">
    <!-- 头像上传区域 -->
    <view class="avatar-section">
      <image 
        class="avatar" 
        :src="formData.avatarUrl || '../../static/default-avatar.jpg'" 
        mode="aspectFill"
        @click="chooseAvatar"
      ></image>
      <text class="avatar-tip">点击上传头像</text>
    </view>
    
    <!-- 个人信息表单 -->
    <view class="form-section">
      <!-- 姓名输入 -->
      <view class="input-group" :class="{ 'error': errors.name }">
        <input 
          class="input-item" 
          type="text" 
          v-model="formData.name" 
          placeholder="请输入姓名"
          @input="validateName"
        />
        <text v-if="errors.name" class="error-text">{{ errors.name }}</text>
      </view>
      
      <!-- 校区选择 -->
      <view class="input-group" :class="{ 'error': errors.campus }">
        <picker 
          class="picker-item" 
          mode="selector" 
          :range="campusOptions" 
          @change="handleCampusChange"
        >
          <view class="picker-content">
            <text>{{ formData.campus || '请选择校区' }}</text>
            <text class="picker-arrow">▼</text>
          </view>
        </picker>
        <text v-if="errors.campus" class="error-text">{{ errors.campus }}</text>
      </view>
      
      <!-- 围合选择 -->
      <view class="input-group" :class="{ 'error': errors.building }">
        <picker 
          class="picker-item" 
          mode="selector" 
          :range="buildingOptions" 
          @change="handleBuildingChange"
        >
          <view class="picker-content">
            <text>{{ formData.building || '请选择围合' }}</text>
            <text class="picker-arrow">▼</text>
          </view>
        </picker>
        <text v-if="errors.building" class="error-text">{{ errors.building }}</text>
      </view>
      
      <!-- 单元选择 -->
      <view class="input-group" :class="{ 'error': errors.unit }">
        <picker 
          class="picker-item" 
          mode="selector" 
          :range="unitOptions" 
          @change="handleUnitChange"
        >
          <view class="picker-content">
            <text>{{ formData.unit || '请选择单元' }}</text>
            <text class="picker-arrow">▼</text>
          </view>
        </picker>
        <text v-if="errors.unit" class="error-text">{{ errors.unit }}</text>
      </view>
      
      <!-- 提交按钮 -->
      <button 
        class="submit-btn" 
        :disabled="!isFormValid" 
        @click="handleSubmit"
      >完成注册</button>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      formData: {
        avatarUrl: '',
        name: '',
        campus: '',
        building: '',
        unit: ''
      },
      errors: {
        name: '',
        campus: '',
        building: '',
        unit: ''
      },
      campusOptions: ['望江校区', '华西校区', '江安校区'],
      buildingOptions: ['1围合', '2围合', '3围合', '4围合', '5围合'],
      unitOptions: ['1单元', '2单元', '3单元', '4单元', '5单元']
    }
  },
  computed: {
    isFormValid() {
      return !Object.values(this.errors).some(error => error) && 
             Object.values(this.formData).every(value => value)
    }
  },
  methods: {
    chooseAvatar() {
      uni.chooseImage({
        count: 1,
        sizeType: ['compressed'],
        sourceType: ['album', 'camera'],
        success: (res) => {
          const tempFilePath = res.tempFilePaths[0]
          
          // 上传图片到服务器
          uni.uploadFile({
            url: 'http://localhost:8080/upload',
            filePath: tempFilePath,
            name: 'file',
            success: (uploadRes) => {
              const data = JSON.parse(uploadRes.data)
              if (data.code == 200) {
                this.formData.avatarUrl = data.url
                uni.showToast({
                  title: '头像上传成功',
                  icon: 'success'
                })
              } else {
                uni.showToast({
                  title: '头像上传失败',
                  icon: 'none'
                })
              }
            },
            fail: () => {
              uni.showToast({
                title: '头像上传失败',
                icon: 'none'
              })
            }
          })
        }
      })
    },
    validateName() {
      if (!this.formData.name) {
        this.errors.name = '请输入姓名'
      } else if (this.formData.name.length < 2) {
        this.errors.name = '姓名至少2个字符'
      } else {
        this.errors.name = ''
      }
    },
    handleCampusChange(e) {
      this.formData.campus = this.campusOptions[e.detail.value]
      this.errors.campus = ''
    },
    handleBuildingChange(e) {
      this.formData.building = this.buildingOptions[e.detail.value]
      this.errors.building = ''
    },
    handleUnitChange(e) {
      this.formData.unit = this.unitOptions[e.detail.value]
      this.errors.unit = ''
    },
    handleSubmit() {
      if (!this.isFormValid) {
        uni.showToast({
          title: '请完善所有信息',
          icon: 'none'
        })
        return
      }
      
      // 发送注册请求
      uni.request({
        url: 'http://localhost:8080/student/register',
        method: 'POST',
        data: this.formData,
        success: (res) => {
          if (res.data.code == 200) {
            uni.showToast({
              title: '注册成功',
              icon: 'success'
            })
            // 注册成功后跳转到登录页
            setTimeout(() => {
              uni.reLaunch({
                url: '/pages/login/index'
              })
            }, 1500)
          } else {
            uni.showToast({
              title: res.data.message || '注册失败',
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
    }
  }
}
</script>

<style lang="scss">
.info-container {
  min-height: 100vh;
  background: linear-gradient(135deg, #f5f7fa 0%, #e4e8f0 100%);
  padding: 40rpx;
  
  .avatar-section {
    display: flex;
    flex-direction: column;
    align-items: center;
    margin-bottom: 80rpx;
    
    .avatar {
      width: 240rpx;
      height: 240rpx;
      border-radius: 120rpx;
      background-color: #fff;
      box-shadow: 0 8rpx 16rpx rgba(0, 0, 0, 0.1);
      margin-bottom: 20rpx;
      transition: all 0.3s ease;
      
      &:active {
        transform: scale(0.95);
        box-shadow: 0 4rpx 8rpx rgba(0, 0, 0, 0.1);
      }
    }
    
    .avatar-tip {
      font-size: 26rpx;
      color: #666;
      opacity: 0.8;
    }
  }
  
  .form-section {
    .input-group {
      margin-bottom: 40rpx;
      position: relative;
      
      &.error {
        .input-item,
        .picker-item {
          border-color: #ff4d4f;
          box-shadow: 0 4rpx 12rpx rgba(255, 77, 79, 0.1);
        }
      }
      
      &.verification-group {
        display: flex;
        gap: 20rpx;
        
        .verification-input {
          flex: 1;
        }
      }
    }
    
    .input-item,
    .picker-item {
      width: 100%;
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
    
    .picker-item {
      .picker-content {
        height: 100rpx;
        display: flex;
        align-items: center;
        justify-content: space-between;
        
        .picker-arrow {
          color: #999;
          font-size: 24rpx;
          transition: transform 0.3s ease;
        }
      }
      
      &:active .picker-arrow {
        transform: rotate(180deg);
      }
    }
    
    .error-text {
      font-size: 24rpx;
      color: #ff4d4f;
      margin-top: 8rpx;
      padding-left: 40rpx;
      animation: shake 0.5s ease;
    }
    
    .submit-btn {
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
