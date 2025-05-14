<template>
  <view class="info-container">
    <!-- 头像上传区域 -->
    <view class="avatar-section">
      <image 
        class="avatar" 
        :src="formData.avatar || '../../static/default-avatar.jpg'" 
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
            <text>{{ campusOptions[formData.campus - 1] || '请选择校区' }}</text>
            <text class="picker-arrow">▼</text>
          </view>
        </picker>
        <text v-if="errors.campus" class="error-text">{{ errors.campus }}</text>
      </view>
      
      <!-- 围合选择 -->
      <view class="input-group" :class="{ 'error': errors.enclosure }">
        <picker 
          class="picker-item" 
          mode="selector" 
          :range="enclosureOptions" 
          @change="handleenclosureChange"
        >
          <view class="picker-content">
            <text>{{ enclosureOptions[formData.enclosure - 1] || '请选择围合' }}</text>
            <text class="picker-arrow">▼</text>
          </view>
        </picker>
        <text v-if="errors.enclosure" class="error-text">{{ errors.enclosure }}</text>
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
            <text>{{ formData.unit ? formData.unit + '单元' : '请选择单元' }}</text>
            <text class="picker-arrow">▼</text>
          </view>
        </picker>
        <text v-if="errors.unit" class="error-text">{{ errors.unit }}</text>
      </view>
      
      <!-- 提交按钮 -->
      <button 
        class="submit-btn" 
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
        username: '',
        studentId: '',
        phone: '',
        password: '',
        avatar: '',
        name: '',
        campus: '',
        enclosure: '',
        unit: ''
      },
      errors: {
        name: '',
        campus: '',
        enclosure: '',
        unit: ''
      },
      campusOptions: ['望江校区', '华西校区', '江安校区'],
      enclosureOptions: ['1围合', '2围合', '3围合', '4围合', '5围合'],
      unitOptions: [1, 2, 3, 4,5]
    }
  },
  onLoad(options) {
    // 从URL参数中获取数据
    if (options.data) {
      const registerData = JSON.parse(decodeURIComponent(options.data))
      this.formData = {
        ...this.formData,
        ...registerData
      }
      console.log(this.formData)
    }
  },
  computed: {
    isFormValid() {
      console.log("检查所有必填字段是否都已填写");
      // 检查所有必填字段是否都已填写
      const hasName = this.formData.name && this.formData.name.length >= 2;
      const hasCampus = this.formData.campus && this.formData.campus >= 1 && this.formData.campus <= 3;
      const hasEnclosure = this.formData.enclosure && this.formData.enclosure >= 1 && this.formData.enclosure <= 5;
      const hasUnit = this.formData.unit && this.formData.unit >= 1 && this.formData.unit <= 5;
      
      return hasName && hasCampus && hasEnclosure && hasUnit;
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
          console.log('选择的图片路径：', tempFilePath);
          
          // 在H5环境下，需要将Blob URL转换为File对象
          if (process.env.UNI_PLATFORM === 'h5') {
            // 从Blob URL获取文件对象
            fetch(tempFilePath)
              .then(response => response.blob())
              .then(blob => {
                // 创建File对象
                const file = new File([blob], 'avatar.jpg', { type: blob.type });
                
                // 创建FormData对象
                const formData = new FormData();
                formData.append('image', file);
                console.log('FormData内容：', formData);
                
                // 使用XMLHttpRequest上传文件
                const xhr = new XMLHttpRequest();
                xhr.open('POST', 'http://localhost:8080/upload', true);
                
                xhr.onload = () => {
                  if (xhr.status === 200) {
                    try {
                      const data = JSON.parse(xhr.responseText);
                      console.log('服务器响应：', data);
                      if (data.code === 1) {
                        this.formData.avatar = data.data;
                        uni.showToast({
                          title: '头像上传成功',
                          icon: 'success'
                        });
                      } else {
                        uni.showToast({
                          title: data.msg || '头像上传失败',
                          icon: 'none'
                        });
                      }
                    } catch (error) {
                      console.error('解析响应数据失败：', error);
                      uni.showToast({
                        title: '头像上传失败',
                        icon: 'none'
                      });
                    }
                  } else {
                    console.error('上传失败，状态码：', xhr.status);
                    uni.showToast({
                      title: '头像上传失败',
                      icon: 'none'
                    });
                  }
                };
                
                xhr.onerror = (error) => {
                  console.error('上传失败：', error);
                  uni.showToast({
                    title: '头像上传失败',
                    icon: 'none'
                  });
                };
                
                xhr.send(formData);
              })
              .catch(error => {
                console.error('处理文件失败：', error);
                uni.showToast({
                  title: '处理文件失败',
                  icon: 'none'
                });
              });
          } else {
            // 非H5环境使用原有的上传方式
            uni.uploadFile({
              url: 'http://localhost:8080/upload',
              filePath: tempFilePath,
              name: 'file',
              success: (uploadRes) => {
                try {
                  const data = JSON.parse(uploadRes.data);
                  console.log('服务器响应：', data);
                  if (data.code === 200) {
                    this.formData.avatar = data.url;
                    uni.showToast({
                      title: '头像上传成功',
                      icon: 'success'
                    });
                  } else {
                    uni.showToast({
                      title: data.msg || '头像上传失败',
                      icon: 'none'
                    });
                  }
                } catch (error) {
                  console.error('解析响应数据失败：', error);
                  uni.showToast({
                    title: '头像上传失败',
                    icon: 'none'
                  });
                }
              },
              fail: (error) => {
                console.error('上传失败：', error);
                uni.showToast({
                  title: '头像上传失败',
                  icon: 'none'
                });
              }
            });
          }
        },
        fail: (error) => {
          console.error('选择图片失败：', error);
          uni.showToast({
            title: '选择图片失败',
            icon: 'none'
          });
        }
      });
    },
    validateName() {
      if (!this.formData.name) {
        this.errors.name = '请输入姓名';
        console.log("请输入姓名");
      } else if (this.formData.name.length < 2) {
        this.errors.name = '姓名至少2个字符';
      } else {
        this.errors.name = '';
      }
    },
    validateCampus() {
      if (!this.formData.campus) {
        this.errors.campus = '请选择校区';
      } else {
        this.errors.campus = '';
      }
    },
    validateEnclosure() {
      if (!this.formData.enclosure) {
        this.errors.enclosure = '请选择围合';
      } else {
        this.errors.enclosure = '';
      }
    },
    validateUnit() {
      if (!this.formData.unit) {
        this.errors.unit = '请选择单元';
      } else {
        this.errors.unit = '';
      }
    },
    handleCampusChange(e) {
      const index = e.detail.value;
      this.formData.campus = index + 1;
      this.validateCampus();
    },
    handleenclosureChange(e) {
      const index = e.detail.value;
      this.formData.enclosure = index + 1;
      this.validateEnclosure();
    },
    handleUnitChange(e) {
      const index = e.detail.value;
      this.formData.unit = index + 1;
      this.validateUnit();
    },
    handleSubmit() {
      // // 提交前进行所有字段的验证
      // this.validateName();
      // this.validateCampus();
      // this.validateEnclosure();
      // this.validateUnit();
      console.log("this.isFormValid");

      if (!this.isFormValid) {
        uni.showToast({
          title: '请完善所有信息',
          icon: 'none'
        });
        return;
      }
      
      // 发送注册请求
      uni.request({
        url: 'http://localhost:8080/student/register',
        method: 'POST',
        data: this.formData,
        success: (res) => {
          if (res.data.code === 1) {
            uni.showToast({
              title: '注册成功',
              icon: 'success'
            });
            // 注册成功后跳转到登录页
            setTimeout(() => {
              uni.redirectTo({
                url: '/pages/login/index'
              });
            }, 1500);
          } else {
            uni.showToast({
              title: res.data.data || '注册失败',
              icon: 'none'
            });
          }
        },
        fail: () => {
          uni.showToast({
            title: '网络错误，请稍后重试',
            icon: 'none'
          });
        }
      });
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
