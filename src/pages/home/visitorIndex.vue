<template>
  <view class="visitor-container">
    <!-- 顶部状态栏 -->
    <view class="status-bar">
      <view class="left">
        <text class="user-status">游客状态</text>
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

      <!-- 排序选项 -->
      <view class="sort-options">
        <view 
          class="sort-item" 
          :class="{ active: currentSort === 'hot' }"
          @click="changeSort('hot')"
        >
          <text>热门</text>
          <view class="underline"></view>
        </view>
        <view 
          class="sort-item" 
          :class="{ active: currentSort === 'new' }"
          @click="changeSort('new')"
        >
          <text>最新</text>
          <view class="underline"></view>
        </view>
      </view>

      <!-- 帖子列表 -->
      <scroll-view 
        class="post-list" 
        scroll-y 
        @scrolltolower="loadMore"
        refresher-enabled
        :refresher-triggered="isRefreshing"
        @refresherrefresh="onRefresh"
      >
        <view class="post-item" v-for="(post, index) in postList" :key="index" @click="onPostClick(post)">
          <!-- 帖子标题 -->
          <view class="post-title">
            <text class="title-tag" v-if="post.isHot">热门</text>
            {{ post.title }}
          </view>
          
          <!-- 帖子内容 -->
          <view class="post-content">{{ post.content }}</view>
          
          <!-- 帖子图片（如果有） -->
          <view class="post-images" v-if="post.images && post.images.length">
            <image 
              v-for="(img, imgIndex) in post.images.slice(0, 3)" 
              :key="imgIndex"
              :src="img"
              mode="aspectFill"
              class="post-image"
              @click.stop="previewImage(post.images, imgIndex)"
            ></image>
            <view class="image-count" v-if="post.images.length > 3">
              +{{ post.images.length - 3 }}
            </view>
          </view>
          
          <!-- 帖子信息 -->
          <view class="post-info">
            <view class="info-left">
              <image class="avatar" :src="post.avatar" mode="aspectFill"></image>
              <text class="username">{{ post.username }}</text>
              <text class="time">{{ post.time }}</text>
            </view>
            <view class="info-right">
              <view class="stat-item">
                <uni-icons type="eye" size="14"></uni-icons>
                <text>{{ post.views }}</text>
              </view>
            </view>
          </view>
        </view>
        
        <!-- 加载更多 -->
        <view class="loading" v-if="isLoading">
          <text>加载中...</text>
        </view>
      </scroll-view>
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
      currentSort: 'hot',
      currentTab: 'home',
      isRefreshing: false,
      isLoading: false,
      bannerList: [
        { imageUrl: '../../static/banner1.jpg' },
        { imageUrl: '../../static/banner2.jpg' },
        { imageUrl: '../../static/banner3.jpg' }
      ],
      postList: [],
      page: 1,
      pageSize: 10,
      tabs: [
        { key: 'home', name: '首页', icon: 'home' },
        { key: 'message', name: '消息', icon: 'chat' },
        { key: 'discover', name: '发现', icon: 'search' },
        { key: 'my', name: '我的', icon: 'person' }
      ]
    }
  },
  onLoad() {
    console.log('Component loaded'); // 调试信息
    this.updateTime()
    this.timer = setInterval(this.updateTime, 1000)
    this.loadPosts()
  },
  onUnload() {
    console.log('Component unloaded'); // 调试信息
    if (this.timer) {
      clearInterval(this.timer)
    }
  },
  methods: {
    updateTime() {
      const now = new Date()
      const hours = now.getHours().toString().padStart(2, '0')
      const minutes = now.getMinutes().toString().padStart(2, '0')
      const seconds = now.getSeconds().toString().padStart(2, '0')
      this.currentTime = `${hours}:${minutes}:${seconds}`
    },
    changeSort(type) {
      if (this.currentSort === type) return
      this.currentSort = type
      this.page = 1
      this.postList = []
      this.loadPosts()
    },
    loadPosts() {
      if (this.isLoading) return
      this.isLoading = true
      
      uni.request({
        url: 'http://localhost:8080/posts/list',
        method: 'GET',
        data: {
          page: this.page,
          pageSize: this.pageSize,
          sortType: this.currentSort
        },
        success: (res) => {
          if (res.data.code === 200) {
            const newPosts = res.data.data
            this.postList = this.page === 1 ? newPosts : [...this.postList, ...newPosts]
            this.page++
          }
        },
        complete: () => {
          this.isLoading = false
          if (this.isRefreshing) {
            this.isRefreshing = false
          }
        }
      })
    },
    loadMore() {
      this.loadPosts()
    },
    onRefresh() {
      this.isRefreshing = true
      this.page = 1
      this.loadPosts()
    },
    previewImage(images, current) {
      uni.previewImage({
        urls: images,
        current: current
      })
    },
    switchTab(key) {
      if (this.currentTab === key) return
      this.currentTab = key
      // 预留tab切换接口
      uni.showToast({
        title: '功能开发中',
        icon: 'none'
      })
    },
    onPostClick(post) {
      // 预留帖子点击接口
      uni.showToast({
        title: '功能开发中',
        icon: 'none'
      })
    }
  }
}
</script>

<style lang="scss">
.visitor-container {
  min-height: 100vh;
  background: linear-gradient(to bottom, #f8f9fa, #e9ecef);
  
  .main-content {
    padding-bottom: 100rpx; // 为底部导航留出空间
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
    
    .user-status {
      font-size: 28rpx;
      color: #333;
      font-weight: 600;
      background: linear-gradient(90deg, #2196F3, #64B5F6);
      -webkit-background-clip: text;
      background-clip: text;
      color: transparent;
      padding: 4rpx 12rpx;
      border-radius: 8rpx;
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
  
  .sort-options {
    display: flex;
    padding: 20rpx 40rpx;
    background: rgba(255, 255, 255, 0.9);
    backdrop-filter: blur(10px);
    border-radius: 20rpx;
    margin: 20rpx;
    box-shadow: 0 4rpx 20rpx rgba(0, 0, 0, 0.05);
    
    .sort-item {
      position: relative;
      margin-right: 60rpx;
      padding: 10rpx 20rpx;
      
      text {
        font-size: 28rpx;
        color: #666;
        transition: all 0.3s ease;
      }
      
      .underline {
        position: absolute;
        bottom: 0;
        left: 50%;
        transform: translateX(-50%);
        width: 0;
        height: 4rpx;
        background: linear-gradient(90deg, #2196F3, #64B5F6);
        border-radius: 2rpx;
        transition: width 0.3s ease;
      }
      
      &.active {
        text {
          color: #2196F3;
          font-weight: 600;
        }
        
        .underline {
          width: 100%;
        }
      }
    }
  }
  
  .post-list {
    height: calc(100vh - 88rpx - 300rpx - 88rpx - 100rpx);
    padding: 0 20rpx;
    
    .post-item {
      margin-bottom: 20rpx;
      padding: 30rpx;
      background: rgba(255, 255, 255, 0.9);
      border-radius: 20rpx;
      box-shadow: 0 4rpx 20rpx rgba(0, 0, 0, 0.05);
      transition: all 0.3s ease;
      
      &:active {
        transform: scale(0.98);
        box-shadow: 0 2rpx 10rpx rgba(0, 0, 0, 0.05);
      }
      
      .post-title {
        font-size: 32rpx;
        font-weight: bold;
        color: #333;
        margin-bottom: 16rpx;
        display: flex;
        align-items: center;
        
        .title-tag {
          font-size: 20rpx;
          color: #fff;
          background: linear-gradient(90deg, #ff4d4f, #ff7875);
          padding: 4rpx 12rpx;
          border-radius: 12rpx;
          margin-right: 12rpx;
        }
      }
      
      .post-content {
        font-size: 28rpx;
        color: #666;
        line-height: 1.6;
        margin-bottom: 20rpx;
      }
      
      .post-images {
        display: flex;
        gap: 12rpx;
        margin-bottom: 20rpx;
        position: relative;
        
        .post-image {
          width: 220rpx;
          height: 220rpx;
          border-radius: 12rpx;
          transition: all 0.3s ease;
          
          &:active {
            transform: scale(0.95);
          }
        }
        
        .image-count {
          position: absolute;
          right: 20rpx;
          bottom: 20rpx;
          background: rgba(0, 0, 0, 0.6);
          color: #fff;
          font-size: 24rpx;
          padding: 6rpx 16rpx;
          border-radius: 20rpx;
          backdrop-filter: blur(4px);
        }
      }
      
      .post-info {
        display: flex;
        justify-content: space-between;
        align-items: center;
        font-size: 24rpx;
        color: #999;
        
        .info-left {
          display: flex;
          align-items: center;
          
          .avatar {
            width: 48rpx;
            height: 48rpx;
            border-radius: 24rpx;
            margin-right: 12rpx;
            border: 2rpx solid #fff;
            box-shadow: 0 2rpx 8rpx rgba(0, 0, 0, 0.1);
          }
          
          .username {
            margin-right: 12rpx;
            color: #666;
            font-weight: 500;
          }
          
          .time {
            color: #bbb;
          }
        }
        
        .info-right {
          .stat-item {
            display: flex;
            align-items: center;
            gap: 6rpx;
            padding: 6rpx 12rpx;
            background: rgba(0, 0, 0, 0.05);
            border-radius: 20rpx;
          }
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
  
  .loading {
    text-align: center;
    padding: 30rpx;
    color: #999;
    font-size: 24rpx;
    
    &::after {
      content: '';
      display: inline-block;
      width: 4rpx;
      height: 4rpx;
      border-radius: 50%;
      background: #999;
      margin-left: 6rpx;
      animation: loading 1s infinite;
    }
  }
}

@keyframes loading {
  0%, 100% { opacity: 0; }
  50% { opacity: 1; }
}
</style> 