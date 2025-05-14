<template>
    <view class="visitor-container">
      <!-- 顶部状态栏 -->
      <view class="status-bar">
        <view class="left">
          <image class="user-avatar" :src="userInfo.avatar || '../../static/default-avatar.jpg'" mode="aspectFill"></image>
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
          <view 
            class="menu-item" 
            v-for="(item, index) in menuItems" 
            :key="index" 
            @click="handleMenuClick(item.key)"
            :class="{ 'menu-item-active': currentMenuKey === item.key }"
          >
            <view class="menu-icon-bg" :class="{ 'menu-icon-bg-active': currentMenuKey === item.key }">
              <uni-icons :type="item.icon" size="32" :color="currentMenuKey === item.key ? '#fff' : '#2196F3'"></uni-icons>
            </view>
            <text class="menu-text" :class="{ 'menu-text-active': currentMenuKey === item.key }">{{ item.name }}</text>
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
          <view class="post-item" v-for="(post, index) in displayPosts" :key="index" @click="onPostClick(post)">
            <!-- 帖子标题 -->
            <view class="post-title">
              <text class="title-tag" v-if="post.isHot">热门</text>
              {{ post.abstractContent }}
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
                <text class="time">{{ post.createTime }}</text>
              </view>
              <view class="info-right">
                <view class="stat-item">
                  <uni-icons type="eye" size="14"></uni-icons>
                  <text>{{ post.readNum }}</text>
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
        token: '',
        currentTime: '',
        timer: null,
        currentSort: 'new',
        currentTab: 'home',
        isRefreshing: false,
        0: false,
        currentModule: '',
        userInfo: {
          id: '',
          userName: '',
          avatar: '',
          sex: '',
          age: '',
          dorm: '',
          describtion: ''
        },
        menuItems: [
          { key: 'help', name: '求助打听', icon: 'location', module: 1 },
          { key: 'secondhand', name: '二手闲置', icon: 'shop', module: 2 },
          { key: 'dating', name: '相亲交友', icon: 'heart', module: 3 },
          { key: 'campus', name: '校园趣事', icon: 'chat', module: 4 },
          { key: 'notice', name: '校园公告', icon: 'calendar', module: 5 }
        ],
        bannerList: [
          { imageUrl: '../../static/banner1.jpg' },
          { imageUrl: '../../static/banner2.jpg' },
          { imageUrl: '../../static/banner3.jpg' }
        ],
      
        postList: [
          {
            id: 1,
            abstractContent: '求购二手自行车',
            content: '想买一辆二手自行车，预算500以内，要求车况良好，有意者请联系',
            createTime: '2024-03-21 14:30',
            isHot: true,
            hotScore: 328,
            module: 2,
            images: [
              '../../static/banner1.jpg',
              '../../static/banner2.jpg'
            ],
            avatar: '../../static/default-avatar.jpg',
            username: '张三',
            readNum: 128
          },
          {
            id: 2,
            abstractContent: '寻找一起考研的研友',
            content: '准备考计算机专业，希望能找到志同道合的研友一起学习，互相督促',
            createTime: '2024-03-21 13:45',
            isHot: false,
            hotScore: 156,
            module: 1,
            images: [],
            avatar: '../../static/default-avatar.jpg',
            username: '李四',
            readNum: 56
          },
          {
            id: 3,
            abstractContent: '校园美食节活动预告',
            content: '本周六将在食堂前广场举办校园美食节，欢迎同学们参加！',
            createTime: '2024-03-21 12:20',
            isHot: true,
            hotScore: 456,
            module: 5,
            images: [
              '../../static/banner3.jpg'
            ],
            avatar: '../../static/default-avatar.jpg',
            username: '学生会',
            readNum: 256
          },
          {
            id: 4,
            abstractContent: '求推荐好的自习室',
            content: '最近图书馆人太多了，想找个安静的自习室，大家有推荐的吗？',
            createTime: '2024-03-21 11:15',
            isHot: false,
            hotScore: 189,
            module: 1,
            images: [],
            avatar: '../../static/default-avatar.jpg',
            username: '王五',
            readNum: 89
          },
          {
            id: 5,
            abstractContent: '二手教材交易',
            content: '出售大二计算机专业教材，九成新，价格可议',
            createTime: '2024-03-21 10:30',
            isHot: false,
            hotScore: 145,
            module: 2,
            images: [
              '../../static/banner1.jpg',
              '../../static/banner2.jpg',
              '../../static/banner3.jpg'
            ],
            avatar: '../../static/default-avatar.jpg',
            username: '赵六',
            readNum: 45
          }
        ],
        lastTime: '',
        filteredPosts: [],
        tabs: [
          { key: 'home', name: '首页', icon: 'home' },
          { key: 'circle', name: '围合', icon: 'chatboxes' },
          { key: 'publish', name: '发布', icon: 'plusempty' },
          { key: 'msg', name: '消息', icon: 'email' },
          { key: 'mine', name: '我的', icon: 'person' }
        ],
        showBackToTop: false,
        scrollTop: 0,
        currentMenuKey: ''
      }
    },
    computed: {
      displayPosts() {
        console.log("重新加载帖子")
        // 使用onfresh会使得postlist清空
        return this.currentModule != '' ? this.filteredPosts : this.postList;
      }
    },
    onLoad() {
      console.log('Component loaded')
      // 获取本地存储的token
      this.token = uni.getStorageSync('token')
      if (!this.token) {
        uni.showToast({
          title: '请先登录',
          icon: 'none'
        })
        // setTimeout(() => {
        //   uni.reLaunch({
        //     url: '/pages/login/index'
        //   })
        // }, 1500)
        
      }
      
      this.updateTime()
      this.timer = setInterval(this.updateTime, 1000)
      console.log("loadUserInfo")
      this.loadUserInfo()
      console.log("loadPosts")
      this.loadPosts()
    },
    onShow() {
      // 页面显示时恢复滚动位置
      if (this.scrollTop > 0) {
        const query = uni.createSelectorQuery().in(this)
        query.select('.post-list').boundingClientRect()
        query.exec((res) => {
          if (res[0]) {
            uni.pageScrollTo({
              scrollTop: this.scrollTop,
              duration: 0
            })
          }
        })
      }
    },
    onHide() {
      // 页面隐藏时保存滚动位置
      const query = uni.createSelectorQuery().in(this)
      query.select('.post-list').boundingClientRect()
      query.exec((res) => {
        if (res[0]) {
          this.scrollTop = res[0].scrollTop
        }
      })
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
        const hours = now.getHours().toString().padStart(2, '0')
        const minutes = now.getMinutes().toString().padStart(2, '0')
        const seconds = now.getSeconds().toString().padStart(2, '0')
        this.currentTime = `${year}-${month}-${day} ${hours}:${minutes}:${seconds}`
        
      },
      handleResponse(res, successCallback) {
        if (res.data.code == 1) {
          if (res.data.msg === 'NOT_LOGIN') {
            uni.showToast({
              title: '登录失效，请重新登录',
              icon: 'none'
            })
            // setTimeout(() => {
            //   uni.reLaunch({
            //     url: '/pages/login/index'
            //   })
            // }, 1500)
            
          }
          successCallback(res.data.data)
        } else {
          uni.showToast({
            title: res.data.msg || '请求失败',
            icon: 'none'
          })
        }
      },
      loadUserInfo() {
        uni.request({
          url: 'http://localhost:8080/student/userInfo',
          method: 'GET',
          header: {
            'token': this.token
          },
          success: (res) => {
            console.log(res.data.data);
            this.handleResponse(res, (data) => {
              console.log("this.userInfo");
              this.userInfo = data
              console.log(this.userInfo);
            })
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
        const selectedItem = this.menuItems.find(item => item.key === key);
        if (selectedItem) {
          // 如果点击的是当前选中的菜单项，则取消选中
          if (this.currentMenuKey === key) {
            this.currentMenuKey = '';
            this.currentModule = '';
            this.filteredPosts = [];
          } else {
            // 否则选中新的菜单项
            this.currentMenuKey = key;
            this.currentModule = selectedItem.module;
            this.filteredPosts = [];
          }
          this.lastTime = ''
          this.postList = []
          this.loadPosts()
        }
      },
      changeSort(type) {
        this.currentSort = type;
        if (type === 'hot') {
          // 按热度评分排序
          this.postList.sort((a, b) => b.hotScore - a.hotScore);
        } else {
          // 按时间排序
          this.postList.sort((a, b) => new Date(b.createTime) - new Date(a.createTime));
        }
      },
      loadPosts() {
        if (this.isLoading) return
        console.log(this.currentTime);
        this.isLoading = true
        
        uni.request({
          url: 'http://localhost:8080/posts/list',
          method: 'GET',
          header: {
            'token': this.token
          },
          data: {
            sortType: this.currentSort,
            module: this.currentModule,
            lastTime: this.lastTime == '' ? this.currentTime : this.lastTime
          },
          success: (res) => {
            this.handleResponse(res, (data) => {
              const newPosts = data
              if (newPosts && newPosts.length > 0) {
                if (this.currentModule !== '') {
                  this.filteredPosts = [...this.filteredPosts, ...newPosts]
                } else {
                  this.postList = [...this.postList, ...newPosts]
                }
                this.lastTime = newPosts[newPosts.length - 1].createTime
                console.log(this.postList);
                console.log(this.lastTime);
              }
            })
          },
          fail: () => {
            uni.showToast({
              title: '获取帖子信息失败',
              icon: 'none'
            })
            console.log("当前帖子列表：");
            console.log(this.postList);
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
        this.isRefreshing = true;
        this.lastTime = '';
        this.postList = [];
        this.currentModule = '';
        this.filteredPosts = [];
        this.loadPosts();
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
          case 'home':
            // 已经在首页，不需要跳转
            break
            
          case 'circle':
            // 跳转到围合页面
            uni.redirectTo({
              url: '/pages/student/dorm/dormIndex'
            })
            break
            
          case 'publish':
            // 跳转到发布页面
            uni.navigateTo({
              url: '/pages/student/publish'
            })
            break
            
          case 'msg':
            // 跳转到消息页面
            uni.redirectTo({
              url: '/pages/message/message'
            })
            break
            
          case 'mine':
            // 跳转到我的页面
            uni.redirectTo({
              url: '/pages/student/myIndex'
            })
            break
        }
      },
      onPostClick(post) {
        // 将整个post对象转换为字符串并传递
        const postData = encodeURIComponent(JSON.stringify(post))
        console.log(postData);
        uni.navigateTo({
        
          url: `/pages/post/detail?post=${postData}`
        })
      },
      onScroll(e) {
        // 当滚动超过300px时显示返回顶部按钮
        this.showBackToTop = e.detail.scrollTop > 300
        // 保存滚动位置
        this.scrollTop = e.detail.scrollTop
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
  @import './studentIndex.scss';
  </style> 