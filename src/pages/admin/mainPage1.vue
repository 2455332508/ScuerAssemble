<template>
  <view class="main-container">
    <!-- 顶部导航栏 -->
    <view class="nav-bar">
      <view class="left">
        <text class="username">张三</text>
      </view>
      <view class="right">
        <text class="date">2025年3月3日 星期五</text>
      </view>
    </view>

    <!-- 功能图标区 -->
    <view class="function-area">
      <view class="function-item" v-for="(item, index) in functions" :key="index">
        <view class="function-icon"></view>
        <text class="function-text">{{ item.name }}</text>
      </view>
    </view>

    <!-- 帖子分类 -->
    <view class="post-category">
      <text :class="['category-item', currentCategory === 'latest' ? 'active' : '']" 
            @click="switchCategory('latest')">最新</text>
      <text :class="['category-item', currentCategory === 'hot' ? 'active' : '']" 
            @click="switchCategory('hot')">热门</text>
    </view>

    <!-- 帖子列表 -->
    <scroll-view scroll-y class="post-list">
      <view class="post-item" v-for="(post, index) in posts" :key="index">
        <view class="post-header">
          <text class="post-tag" v-if="post.isHot">热门</text>
          <text class="post-title">{{ post.title }}</text>
        </view>
        <view class="post-content">{{ post.content }}</view>
        <view class="post-footer">
          <view class="user-info">
            <image class="avatar" :src="post.avatar"></image>
            <text class="username">{{ post.username }}</text>
            <text class="time">{{ post.time }}</text>
          </view>
          <view class="view-count">
            <uni-icons type="eye" size="12"></uni-icons>
            <text>{{ post.views }}</text>
          </view>
        </view>
      </view>
    </scroll-view>

    <!-- 底部导航栏 -->
    <view class="tab-bar">
      <!-- 左侧两个tab -->
      <view class="tab-item" v-for="(tab, index) in leftTabs" :key="index"
            :class="{ active: currentTab === tab.key }"
            @click="switchTab(tab.key)">
        <uni-icons :type="tab.icon" size="20"></uni-icons>
        <text>{{ tab.name }}</text>
      </view>

      <!-- 发布按钮 -->
      <view class="publish-container">
        <view class="publish-btn" @click="goToPublish">
          <uni-icons type="plus" size="28" color="#fff"></uni-icons>
        </view>
        <text class="publish-text">发布</text>
      </view>

      <!-- 右侧两个tab -->
      <view class="tab-item" v-for="(tab, index) in rightTabs" :key="index"
            :class="{ active: currentTab === tab.key }"
            @click="switchTab(tab.key)">
        <uni-icons :type="tab.icon" size="20"></uni-icons>
        <text>{{ tab.name }}</text>
      </view>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      currentCategory: 'latest',
      currentTab: 'home',
      functions: [
        { name: '求助打听' },
        { name: '二手闲置' },
        { name: '科学交流' },
        { name: '社区表白' },
        { name: '校园公告' }
      ],
      posts: [
        {
          isHot: true,
          title: '四川大学校招会正在火热进行中！！！',
          content: '由于平年冬季来临，学校xxxxxx......',
          avatar: '/static/avatar.png',
          username: '天涯小丁子',
          time: '12月13日 22:22',
          views: 5692
        }
        // 可以添加更多帖子数据
      ],
      leftTabs: [
        { key: 'home', name: '首页', icon: 'home' },
        { key: 'message', name: '消息', icon: 'chat' }
      ],
      rightTabs: [
        { key: 'notification', name: '通知', icon: 'notification' },
        { key: 'my', name: '我的', icon: 'person' }
      ]
    }
  },
  methods: {
    switchCategory(category) {
      this.currentCategory = category
      // 这里可以添加切换分类的逻辑
    },
    switchTab(key) {
      this.currentTab = key
      // 这里可以添加切换标签页的逻辑
    },
    goToPublish() {
      uni.navigateTo({
        url: '/pages/publish/publish'
      })
    }
  }
}
</script>

<style lang="scss">
.main-container {
  min-height: 100vh;
  background-color: #f5f5f5;
  padding-bottom: calc(100rpx + env(safe-area-inset-bottom));
}

.nav-bar {
  background-color: #4080ff;
  color: #fff;
  padding: 20rpx 30rpx;
  display: flex;
  justify-content: space-between;
  align-items: center;
  
  .date {
    font-size: 28rpx;
  }
  
  .right {
    padding: 10rpx;
  }
}

.function-area {
  background-color: #fff;
  padding: 30rpx 20rpx;
  display: flex;
  justify-content: space-around;
  
  .function-item {
    display: flex;
    flex-direction: column;
    align-items: center;
    
    .function-icon {
      width: 80rpx;
      height: 80rpx;
      background-color: #eee;
      border-radius: 50%;
      margin-bottom: 10rpx;
    }
    
    .function-text {
      font-size: 24rpx;
      color: #333;
    }
  }
}

.post-category {
  background-color: #fff;
  padding: 20rpx 30rpx;
  display: flex;
  gap: 40rpx;
  border-bottom: 1px solid #eee;
  
  .category-item {
    font-size: 28rpx;
    color: #666;
    position: relative;
    padding-bottom: 10rpx;
    
    &.active {
      color: #4080ff;
      font-weight: bold;
      
      &::after {
        content: '';
        position: absolute;
        bottom: 0;
        left: 0;
        width: 100%;
        height: 4rpx;
        background-color: #4080ff;
        border-radius: 2rpx;
      }
    }
  }
}

.post-list {
  padding: 20rpx;
  
  .post-item {
    background-color: #fff;
    border-radius: 12rpx;
    padding: 20rpx;
    margin-bottom: 20rpx;
    
    .post-header {
      display: flex;
      align-items: center;
      margin-bottom: 16rpx;
      
      .post-tag {
        background-color: #ff4d4f;
        color: #fff;
        font-size: 20rpx;
        padding: 4rpx 12rpx;
        border-radius: 8rpx;
        margin-right: 12rpx;
      }
      
      .post-title {
        font-size: 28rpx;
        font-weight: bold;
        color: #333;
      }
    }
    
    .post-content {
      font-size: 26rpx;
      color: #666;
      line-height: 1.6;
      margin-bottom: 16rpx;
    }
    
    .post-footer {
      display: flex;
      justify-content: space-between;
      align-items: center;
      
      .user-info {
        display: flex;
        align-items: center;
        gap: 12rpx;
        
        .avatar {
          width: 40rpx;
          height: 40rpx;
          border-radius: 50%;
        }
        
        .username {
          font-size: 24rpx;
          color: #666;
        }
        
        .time {
          font-size: 24rpx;
          color: #999;
        }
      }
      
      .view-count {
        display: flex;
        align-items: center;
        gap: 4rpx;
        font-size: 24rpx;
        color: #999;
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
  background-color: #fff;
  display: flex;
  justify-content: space-around;
  align-items: center;
  border-top: 1px solid #eee;
  padding-bottom: env(safe-area-inset-bottom);
  
  .tab-item {
    flex: 1;
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 4rpx;
    
    text {
      font-size: 20rpx;
      color: #666;
    }
    
    &.active {
      color: #4080ff;
      
      text {
        color: #4080ff;
      }
    }
  }

  .publish-container {
    display: flex;
    flex-direction: column;
    align-items: center;
    
    .publish-btn {
      width: 120rpx;
      height: 120rpx;
      background: linear-gradient(135deg, #42b883, #42b983);
      border-radius: 50%;
      display: flex;
      align-items: center;
      justify-content: center;
      margin-bottom: 8rpx; // 调整按钮和文字的间距
      box-shadow: 0 4rpx 20rpx rgba(66, 185, 131, 0.3);
      transition: all 0.3s ease;
      transform: translateY(-40rpx); // 使用 transform 替代 margin-bottom 实现凸起效果
      
      &:active {
        transform: translateY(-36rpx) scale(0.9); // 点击时保持凸起效果
      }
    }
    
    .publish-text {
      font-size: 20rpx;
      color: #42b983; // 使用与按钮相同的颜色
      transform: translateY(-40rpx); // 文字也需要向上偏移
    }
  }
}
</style>
