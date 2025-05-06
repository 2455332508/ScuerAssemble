<template>
    <view class="visitor-container">
      <!-- 顶部状态栏 -->
      <view class="status-bar">
        <view class="left">
          <image class="user-avatar" :src="userInfo.photo || '../../static/default-avatar.jpg'" mode="aspectFill"></image>
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
  
        <!-- 功能菜单栏 -->
        <view class="menu-bar">
          <view class="menu-item" v-for="(item, index) in menuItems" :key="index" @click="handleMenuClick(item.key)">
            <view class="menu-icon-bg">
              <uni-icons :type="item.icon" size="32" color="#2196F3"></uni-icons>
            </view>
            <text class="menu-text">{{ item.name }}</text>
          </view>
        </view>
  
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
          @scroll="onScroll"
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

        <!-- 返回顶部按钮 -->
        <view class="back-to-top" v-if="showBackToTop" @click="scrollToTop">
          <uni-icons type="top" size="24" color="#fff"></uni-icons>
        </view>
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
          <view class="tab-icon-bg" :class="{ 'tab-publish': tab.key === 'publish' }">
            <uni-icons :type="tab.icon" :size="tab.key === 'publish' ? 36 : 26" :color="currentTab === tab.key ? (tab.key === 'publish' ? '#fff' : '#2196F3') : (tab.key === 'publish' ? '#2196F3' : '#999')"></uni-icons>
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
        currentModule: '',
        userInfo: {
          userName: '',
          photo: '',
          sex: '',
          age: '',
          dorm: '',
          describtion: ''
        },
        menuItems: [
          { key: 'help', name: '求助打听', icon: 'location', module: 'HELP' },
          { key: 'secondhand', name: '二手闲置', icon: 'shop', module: 'SECONDHAND' },
          { key: 'dating', name: '相亲交友', icon: 'heart', module: 'DATING' },
          { key: 'campus', name: '校园趣事', icon: 'chat', module: 'CAMPUS' },
          { key: 'notice', name: '校园公告', icon: 'calendar', module: 'NOTICE' }
        ],
        bannerList: [
          { imageUrl: '../../static/banner1.jpg' },
          { imageUrl: '../../static/banner2.jpg' },
          { imageUrl: '../../static/banner3.jpg' }
        ],
        postList: [
          {
            title: '求购二手自行车',
            content: '想买一辆二手自行车，预算500以内，要求车况良好，有意者请联系',
            time: '2024-03-21 14:30',
            isHot: true,
            images: [
              '../../static/banner1.jpg',
              '../../static/banner2.jpg'
            ],
            avatar: '../../static/default-avatar.jpg',
            username: '张三',
            views: 128
          },
          {
            title: '寻找一起考研的研友',
            content: '准备考计算机专业，希望能找到志同道合的研友一起学习，互相督促',
            time: '2024-03-21 13:45',
            isHot: false,
            images: [],
            avatar: '../../static/default-avatar.jpg',
            username: '李四',
            views: 56
          },
          {
            title: '校园美食节活动预告',
            content: '本周六将在食堂前广场举办校园美食节，欢迎同学们参加！',
            time: '2024-03-21 12:20',
            isHot: true,
            images: [
              '../../static/banner3.jpg'
            ],
            avatar: '../../static/default-avatar.jpg',
            username: '学生会',
            views: 256
          },
          {
            title: '求推荐好的自习室',
            content: '最近图书馆人太多了，想找个安静的自习室，大家有推荐的吗？',
            time: '2024-03-21 11:15',
            isHot: false,
            images: [],
            avatar: '../../static/default-avatar.jpg',
            username: '王五',
            views: 89
          },
          {
            title: '二手教材交易',
            content: '出售大二计算机专业教材，九成新，价格可议',
            time: '2024-03-21 10:30',
            isHot: false,
            images: [
              '../../static/banner1.jpg',
              '../../static/banner2.jpg',
              '../../static/banner3.jpg'
            ],
            avatar: '../../static/default-avatar.jpg',
            username: '赵六',
            views: 45
          }
        ],
        lastTime: '',
        tabs: [
          { key: 'home', name: '首页', icon: 'home' },
          { key: 'circle', name: '围合', icon: 'chatboxes' },
          { key: 'publish', name: '发布', icon: 'plusempty' },
          { key: 'msg', name: '消息', icon: 'email' },
          { key: 'mine', name: '我的', icon: 'person' }
        ],
        showBackToTop: false
      }
    },
    onLoad() {
      console.log('Component loaded'); // 调试信息
      this.updateTime()
      this.timer = setInterval(this.updateTime, 1000)
      this.loadUserInfo()
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
        const year = now.getFullYear()
        const month = (now.getMonth() + 1).toString().padStart(2, '0')
        const day = now.getDate().toString().padStart(2, '0')
        const weekdays = ['星期日', '星期一', '星期二', '星期三', '星期四', '星期五', '星期六']
        const weekday = weekdays[now.getDay()]
        this.currentTime = `${year}年${month}月${day}日 ${weekday}`
      },
      loadUserInfo() {
        uni.request({
          url: 'http://localhost:8080/student/userInfo',
          method: 'GET',
          success: (res) => {
            if (res.data.code === 200) {
              this.userInfo = res.data.data
            }
          },
          fail: (err) => {
            console.error('获取用户信息失败：', err)
            uni.showToast({
              title: '获取用户信息失败',
              icon: 'none'
            })
          }
        })
      },
      handleMenuClick(key) {
        const selectedItem = this.menuItems.find(item => item.key === key)
        if (selectedItem) {
          if (this.currentModule === selectedItem.module) {
            this.currentModule = ''
          } else {
            this.currentModule = selectedItem.module
          }
          this.lastTime = ''
          this.postList = []
          this.loadPosts()
        }
      },
      changeSort(type) {
        if (this.currentSort === type) return
        this.currentSort = type
        this.lastTime = ''
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
            sortType: this.currentSort,
            module: this.currentModule,
            lastTime: this.lastTime
          },
          success: (res) => {
            if (res.data.code === 200) {
              const newPosts = res.data.data
              if (newPosts && newPosts.length > 0) {
                this.postList = [...this.postList, ...newPosts]
                this.lastTime = newPosts[newPosts.length - 1].time
              }
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
        this.lastTime = ''
        this.postList = []
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
        
        switch (key) {
          case 'back':
            uni.navigateBack({
              delta: 1,
              fail: () => {
                uni.showToast({
                  title: '已经是第一页了',
                  icon: 'none'
                })
              }
            })
            break
            
          case 'login':
            uni.redirectTo({
              url: '/pages/login/index'
            })
            break
            
          case 'navigation':
          uni.redirectTo({
              url: '/pages/map/Map'
            })
            break
            
          case 'about':
          uni.redirectTo({
              url: '/pages/about/aboutUs'
            })
            break
        }
      },
      onPostClick(post) {
        // 预留帖子点击接口
        uni.showToast({
          title: '功能开发中',
          icon: 'none'
        })
      },
      onScroll(e) {
        // 当滚动超过300px时显示返回顶部按钮
        this.showBackToTop = e.detail.scrollTop > 300
      },
      scrollToTop() {
        // 获取scroll-view组件实例
        const query = uni.createSelectorQuery().in(this)
        query.select('.post-list').boundingClientRect()
        query.exec((res) => {
          if (res[0]) {
            // 滚动到顶部
            uni.pageScrollTo({
              scrollTop: 0,
              duration: 300
            })
          }
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
      position: relative; // 为返回顶部按钮定位
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
      
      .user-avatar {
        width: 40px;
        height: 40px;
        border-radius: 50%;
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
    
    .menu-bar {
      display: flex;
      justify-content: space-around;
      padding: 15px 0;
      background-color: #fff;
      margin: 10px 0;
      border-radius: 8px;
    }
    
    .menu-item {
      display: flex;
      flex-direction: column;
      align-items: center;
      width: 20%;
      position: relative;
      
      &.active {
        .menu-icon-bg {
          background: #2196F3;
          .uni-icons {
            color: #fff !important;
          }
        }
        .menu-text {
          color: #2196F3;
          font-weight: 500;
        }
      }
    }
    
    .menu-icon-bg {
      width: 48px;
      height: 48px;
      background: #e3f2fd;
      border-radius: 50%;
      display: flex;
      align-items: center;
      justify-content: center;
      margin-bottom: 6px;
    }
    
    .menu-text {
      font-size: 14px;
      color: #333;
      margin-top: 5px;
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
      height: calc(100vh - 88rpx - 100rpx);
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
        flex: 1;
      }
      
      .tab-icon-bg {
        width: 44px;
        height: 44px;
        display: flex;
        align-items: center;
        justify-content: center;
        border-radius: 50%;
        margin-bottom: 4px;
      }
      
      .tab-publish {
        background: linear-gradient(90deg, #2196F3, #64B5F6);
        box-shadow: 0 2px 8px #2196f355;
      }
      
      .tab-text {
        font-size: 24rpx;
        color: #999;
        transition: color 0.3s ease;
      }
      
      .tab-item.active {
        .tab-text {
          color: #2196F3;
          font-weight: 500;
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

    .back-to-top {
      position: fixed;
      right: 30rpx;
      bottom: 120rpx;
      width: 80rpx;
      height: 80rpx;
      background: rgba(33, 150, 243, 0.9);
      border-radius: 50%;
      display: flex;
      align-items: center;
      justify-content: center;
      box-shadow: 0 4rpx 20rpx rgba(33, 150, 243, 0.3);
      z-index: 99;
      transition: all 0.3s ease;
      
      &:active {
        transform: scale(0.9);
      }
    }
  }
  
  @keyframes loading {
    0%, 100% { opacity: 0; }
    50% { opacity: 1; }
  }
  </style> 