<template>
  <view class="profile-bg">
    <view class="back-btn" @click="goBack">
      <uni-icons type="back" size="24" color="#333" />
      <text>返回</text>
    </view>
    <view class="profile-card">
      <view class="profile-header">
        <image :src="user.avatar || defaultAvatar" class="avatar"></image>
        <button class="avatar-btn">修改头像</button>
      </view>
      <view class="profile-info">
        <!-- 用户名 -->
        <view class="info-item">
          <text class="label">用户名</text>
          <template v-if="editField === 'username'">
            <input v-model="editValue" class="edit-input" />
            <button class="save-btn" @click="saveEdit('username')">保存</button>
            <button class="cancel-btn" @click="cancelEdit">取消</button>
          </template>
          <template v-else>
            <text class="value">{{ user.username }}</text>
            <uni-icons type="compose" size="20" class="edit-icon" @click="startEdit('username', user.username)" />
          </template>
        </view>
        <!-- 性别 -->
        <view class="info-item">
          <text class="label">性别</text>
          <text class="value">{{ genderText }}</text>
        </view>
        <!-- 学号 -->
        <view class="info-item">
          <text class="label">学号</text>
          <text class="value">{{ user.studentId }}</text>
        </view>
        <!-- 手机号 -->
        <view class="info-item">
          <text class="label">手机号</text>
          <template v-if="editField === 'phone'">
            <input v-model="editValue" class="edit-input" />
            <button class="save-btn" @click="saveEdit('phone')">保存</button>
            <button class="cancel-btn" @click="cancelEdit">取消</button>
          </template>
          <template v-else>
            <text class="value">{{ user.phone }}</text>
            <uni-icons type="compose" size="20" class="edit-icon" @click="startEdit('phone', user.phone)" />
          </template>
        </view>
        <!-- 其他字段同理... -->
        <view class="info-item">
          <text class="label">校区</text>
          <text class="value">{{ user.campus }}</text>
        </view>
        <view class="info-item">
          <text class="label">单位</text>
          <text class="value">{{ user.unit }}</text>
        </view>
        <view class="info-item">
          <text class="label">围合</text>
          <text class="value">{{ user.enclosure }}</text>
        </view>
        <view class="info-item">
          <text class="label">注册时间</text>
          <text class="value">{{ user.createTime }}</text>
        </view>
      </view>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      user: {},
      defaultAvatar: '/static/default-avatar.jpg',
      editField: '', // 当前编辑的字段名
      editValue: '', // 编辑时的值
    }
  },
  computed: {
    genderText() {
      // 这里假设status为性别，0-未知，1-男，2-女
      if (this.user.status === 1) return '男'
      if (this.user.status === 2) return '女'
      return '未知'
    }
  },
  onLoad() {
    this.fetchUserInfo()
  },
  methods: {
    goBack() {
      //   uni.redirectTo({
      // url: '/pages/about/aboutUs'
      uni.navigateBack({
        delta: 1
    })
    },

    fetchUserInfo() {
      const token = uni.getStorageSync('token')
      uni.request({
        url: 'http://localhost:8080/student/userInfo',
        method: 'GET',
        header: {
          'token': token
        },
        success: (res) => {
          if (res.data && res.data.data) {
            this.user = res.data.data
          } else {
            uni.showToast({ title: '获取用户信息失败', icon: 'none' })
          }
        },
        fail: () => {
          uni.showToast({ title: '网络错误', icon: 'none' })
        }
      })
    },
    startEdit(field, value) {
      this.editField = field
      this.editValue = value
    },
    cancelEdit() {
      this.editField = ''
      this.editValue = ''
    },
    saveEdit(field) {
      const token = uni.getStorageSync('token')
      const updateData = { ...this.user, [field]: this.editValue }
      uni.request({
        url: 'http://localhost:8080/student/updata',
        method: 'PUT',
        header: { 'token': token, 'Content-Type': 'application/json' },
        data: updateData,
        success: (res) => {
          if (res.data && res.data.code === 1) {
            this.user[field] = this.editValue
            this.cancelEdit()
            uni.showToast({ title: '修改成功', icon: 'success' })
          } else {
            uni.showToast({ title: '修改失败', icon: 'none' })
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

<style scoped>
.profile-bg {
  min-height: 100vh;
  background: linear-gradient(135deg, #e0e7ff 0%, #fff 100%);
  padding-top: 40rpx;
}
.profile-card {
  max-width: 700rpx;
  margin: 40rpx auto;
  background: #fff;
  border-radius: 24rpx;
  box-shadow: 0 8rpx 32rpx rgba(60, 60, 120, 0.08);
  padding: 10 0 40rpx 0;
  overflow: hidden;
}
.profile-header {
  display: flex;
  flex-direction: column;
  align-items: center;
  background: linear-gradient(135deg, #a5b4fc 0%, #f0f4ff 100%);
  padding: 40rpx 0 24rpx 0;
  border-bottom: 1px solid #f0f0f0;
}
.avatar {
  width: 120rpx;
  height: 120rpx;
  border-radius: 50%;
  background: #f5f5f5;
  border: 4rpx solid #fff;
  box-shadow: 0 4rpx 16rpx rgba(60, 60, 120, 0.08);
}
.avatar-btn {
  margin-top: 16rpx;
  font-size: 24rpx;
  color: #4f46e5;
  background: #e0e7ff;
  border: none;
  border-radius: 12rpx;
  padding: 8rpx 24rpx;
  transition: background 0.2s;
}
.avatar-btn:active {
  background: #c7d2fe;
}
.profile-info {
  margin: 0 32rpx;
  margin-top: 24rpx;
}
.info-item {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 18rpx 0;
  border-bottom: 1px solid #f0f0f0;
  font-size: 30rpx;
  position: relative;
}
.label {
  color: #888;
  font-weight: 500;
}
.value {
  color: #333;
  margin-right: 16rpx;
}
.edit-icon {
  color: #4f46e5;
  margin-left: 8rpx;
  cursor: pointer;
}
.edit-input {
  border: 1px solid #a5b4fc;
  border-radius: 8rpx;
  padding: 4rpx 12rpx;
  font-size: 28rpx;
  width: 200rpx;
  margin-right: 8rpx;
}
.save-btn, .cancel-btn {
  font-size: 24rpx;
  margin-left: 8rpx;
  border: none;
  border-radius: 8rpx;
  padding: 4rpx 16rpx;
  transition: background 0.2s;
}
.save-btn {
  background: #4f46e5;
  color: #fff;
}
.save-btn:active {
  background: #6366f1;
}
.cancel-btn {
  background: #e5e7eb;
  color: #333;
}
.cancel-btn:active {
  background: #d1d5db;
}
.back-btn {
  position: fixed;
  top: 24rpx;
  left: 24rpx;
  z-index: 10;
  display: flex;
  align-items: center;
  cursor: pointer;
  background: #fff;
  border-radius: 16rpx;
  box-shadow: 0 2rpx 8rpx rgba(60,60,120,0.06);
  padding: 8rpx 18rpx;
}
</style>
