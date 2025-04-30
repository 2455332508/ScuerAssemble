<template>
  <view class="publish-container">
    <!-- 顶部导航栏 -->
    <view class="nav-bar">
      <view class="left" @click="goBack">
        <uni-icons type="left" size="20" color="#000"></uni-icons>
      </view>
      <view class="title">公告消息</view>
      <view class="right"></view>
    </view>

    <!-- 表单内容 -->
    <view class="form-content">
      <!-- 标题输入 -->
      <view class="form-item">
        <view class="label">标题</view>
        <input 
          class="input-box" 
          type="text" 
          v-model="title" 
          placeholder="请输入"
          placeholder-style="color: #999;"
        />
      </view>

      <!-- 内容输入 -->
      <view class="form-item">
        <textarea 
          class="content-input" 
          v-model="content"
          placeholder="如：本群为E网生活基地区（某小区或某基镇）社区群，欢迎加入这个大家庭！"
          placeholder-style="color: #999;"
          :maxlength="150"
        />
        <view class="word-count">已输入 {{ content.length }}/150个字</view>
      </view>

      <!-- 时间选择 -->
      <view class="time-section">
        <view class="time-row">
          <text>时间</text>
          <view class="time-inputs">
            <picker 
              mode="date" 
              :value="startDate" 
              @change="onStartDateChange"
              class="date-picker"
            >
              <view class="picker-text">{{ startDate }}</view>
            </picker>
            <text class="separator">到</text>
            <picker 
              mode="date" 
              :value="endDate" 
              @change="onEndDateChange"
              class="date-picker"
            >
              <view class="picker-text">{{ endDate }}</view>
            </picker>
          </view>
        </view>
      </view>
    </view>

    <!-- 发布按钮 -->
    <view class="submit-btn" @click="submitForm">发布</view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      title: '',
      content: '',
      startDate: '2022-05-05',
      endDate: '2022-05-05'
    }
  },
  methods: {
    goBack() {
      uni.navigateBack()
    },
    onStartDateChange(e) {
      this.startDate = e.detail.value
    },
    onEndDateChange(e) {
      this.endDate = e.detail.value
    },
    submitForm() {
      if (!this.title.trim()) {
        uni.showToast({
          title: '请输入标题',
          icon: 'none'
        })
        return
      }
      if (!this.content.trim()) {
        uni.showToast({
          title: '请输入内容',
          icon: 'none'
        })
        return
      }
      // TODO: 实现发布逻辑
      uni.showToast({
        title: '发布成功',
        icon: 'success'
      })
    }
  }
}
</script>

<style lang="scss">
.publish-container {
  min-height: 100vh;
  background-color: #f5f5f5;
}

.nav-bar {
  background-color: #fff;
  height: 88rpx;
  display: flex;
  align-items: center;
  padding: 0 30rpx;
  padding-top: var(--status-bar-height);
  
  .left {
    width: 60rpx;
    height: 60rpx;
    display: flex;
    align-items: center;
  }
  
  .title {
    flex: 1;
    text-align: center;
    font-size: 32rpx;
    font-weight: 500;
  }
  
  .right {
    width: 60rpx;
  }
}

.form-content {
  margin-top: 20rpx;
  background-color: #fff;
  padding: 30rpx;
  
  .form-item {
    margin-bottom: 30rpx;
    
    .label {
      font-size: 28rpx;
      color: #333;
      margin-bottom: 20rpx;
    }
    
    .input-box {
      width: 100%;
      height: 80rpx;
      border: 1px solid #eee;
      border-radius: 8rpx;
      padding: 0 20rpx;
      font-size: 28rpx;
    }
    
    .content-input {
      width: 100%;
      height: 300rpx;
      border: 1px solid #eee;
      border-radius: 8rpx;
      padding: 20rpx;
      font-size: 28rpx;
    }
    
    .word-count {
      text-align: right;
      font-size: 24rpx;
      color: #999;
      margin-top: 10rpx;
    }
  }
}

.time-section {
  background-color: #fff;
  
  .time-row {
    display: flex;
    align-items: center;
    padding: 20rpx 0;
    
    text {
      font-size: 28rpx;
      color: #333;
      margin-right: 30rpx;
    }
    
    .time-inputs {
      flex: 1;
      display: flex;
      align-items: center;
      
      .date-picker {
        flex: 1;
        
        .picker-text {
          height: 80rpx;
          line-height: 80rpx;
          border: 1px solid #eee;
          border-radius: 8rpx;
          padding: 0 20rpx;
          font-size: 28rpx;
          color: #333;
        }
      }
      
      .separator {
        margin: 0 20rpx;
        color: #666;
      }
    }
  }
}

.submit-btn {
  position: fixed;
  bottom: 40rpx;
  left: 30rpx;
  right: 30rpx;
  height: 90rpx;
  background-color: #4080ff;
  color: #fff;
  font-size: 32rpx;
  display: flex;
  align-items: center;
  justify-content: center;
  border-radius: 45rpx;
  
  &:active {
    opacity: 0.9;
  }
}
</style>
