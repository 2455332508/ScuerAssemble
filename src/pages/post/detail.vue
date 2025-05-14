<template>
  <view class="detail-container">
    <!-- 顶部导航栏 -->
    <view class="nav-bar">
      <view class="back-btn" @click="goBack">
        <uni-icons type="back" size="24" color="#333"></uni-icons>
        <text>返回</text>
      </view>
    </view>

    <!-- 帖子头部 -->
    <view class="detail-header">
      <image :src="detail.avatar" class="avatar"></image>
      <view class="user-info">
        <text class="username">{{ detail.username }}</text>
        <text class="time">{{ detail.createTime }}</text>
      </view>
      <button class="report-btn" @click="reportPost">举报</button>
    </view>

    <!-- 帖子内容 -->
    <view class="detail-title">{{ detail.abstractContent }}</view>
    <view class="detail-content">{{ detail.detail }}</view>
    <view class="detail-images" v-if="detail.images && detail.images.length">
      <image
        v-for="(img, idx) in detail.images"
        :key="idx"
        :src="img"
        class="detail-image"
        @click="previewImage(idx)"
      />
    </view>

    <!-- 点赞/评论/浏览数 -->
    <view class="detail-actions">
      <view class="action-item" @click="likePost">
        <uni-icons type="hand-up" size="22" :color="liked ? '#2196F3' : '#999'"/>
        <text>{{ detail.likeNum }}</text>
      </view>
      <view class="action-item">
        <uni-icons type="eye" size="22"/>
        <text>{{ detail.readNum }}</text>
      </view>
      <view class="action-item">
        <uni-icons type="chat" size="22"/>
        <text>{{ comments.length }}</text>
      </view>
    </view>

    <!-- 评论区 -->
    <view class="comments-section">
      <view class="comment-item" v-for="(c, idx) in comments" :key="idx">
        <image :src="c.avatar" class="comment-avatar"/>
        <view class="comment-content">
          <text class="comment-username">{{ c.username }}</text>
          <text class="comment-text">{{ c.text }}</text>
          <text class="comment-time">{{ c.createTime }}</text>
        </view>
      </view>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      detail: {},
      comments: [],
      liked: false,
      id: null,
      post: null
    }
  },
  onLoad(options) {
    if (options.post) {
      // 解析传递过来的post数据
      this.post = JSON.parse(decodeURIComponent(options.post))
      console.log('解析传递过来的post数据'+this.post.id)
      this.id = this.post.id
      // 使用post数据初始化页面
      this.initDetailFromPost()
    }
    this.fetchDetail()
  },
  methods: {
    initDetailFromPost() {
      // 使用post数据初始化detail对象
      this.detail = {
        ...this.post,
        likeNum: this.post.likeNum || 0,
        readNum: this.post.readNum || 0,
        liked: false
      }
    },
    fetchDetail() {
      // 从本地存储获取token
      const token = uni.getStorageSync('token')
      
      // 发送请求到后端
      uni.request({
        url: 'http://localhost:8080/posts/detail',
        method: 'GET',
        data: {
          id: this.id
        },
        header: {
          'token': token
        },
        success: (res) => {
          if (res.data.code === 1) {
            console.log(res.data.data);
            // 更新页面数据
            this.detail = res.data.data
            this.comments = res.data.data.comments || []
            this.liked = res.data.data.liked || false
          } else {
            // 如果请求失败，使用测试数据
            console.log(res.data.data);
            this.useMockData()
          }
        },
        fail: () => {
          // 如果请求失败，使用测试数据
          this.useMockData()
        }
      })
    },
    
    // 使用测试数据的方法
    useMockData() {
      // 模拟后端请求延迟
      setTimeout(() => {
        // 根据id生成不同的测试数据
        const mockData = {
          '1': {
            abstractContent: '求购二手自行车',
            detail: '想买一辆二手自行车，预算500以内，要求车况良好，有意者请联系。最好是捷安特或者美利达的，车龄不超过3年。',
            images: [
              '../../static/banner1.jpg',
              '../../static/banner2.jpg'
            ],
            avatar: '../../static/default-avatar.jpg',
            username: '张三',
            createTime: '2024-03-21 14:30',
            likeNum: 15,
            readNum: 128,
            liked: false,
            comments: [
              {
                avatar: '../../static/default-avatar.jpg',
                username: '李四',
                text: '我有一辆捷安特，去年买的，要不要看看？',
                createTime: '2024-03-21 14:35'
              },
              {
                avatar: '../../static/default-avatar.jpg',
                username: '王五',
                text: '预算有点低，建议提高一点预算',
                createTime: '2024-03-21 14:40'
              }
            ]
          },
          '2': {
            abstractContent: '寻找一起考研的研友',
            detail: '准备考计算机专业，希望能找到志同道合的研友一起学习，互相督促。本人目前大三，目标院校是川大计算机学院。',
            images: [],
            avatar: '../../static/default-avatar.jpg',
            username: '李四',
            createTime: '2024-03-21 13:45',
            likeNum: 28,
            readNum: 56,
            liked: true,
            comments: [
              {
                avatar: '../../static/default-avatar.jpg',
                username: '赵六',
                text: '我也是准备考计算机，可以一起学习',
                createTime: '2024-03-21 13:50'
              }
            ]
          },
          '3': {
            abstractContent: '校园美食节活动预告',
            detail: '本周六将在食堂前广场举办校园美食节，欢迎同学们参加！活动时间：上午10点至下午4点。现场将有各种特色小吃，还有抽奖活动！',
            images: [
              '../../static/banner3.jpg'
            ],
            avatar: '../../static/default-avatar.jpg',
            username: '学生会',
            createTime: '2024-03-21 12:20',
            likeNum: 156,
            readNum: 256,
            liked: false,
            comments: [
              {
                avatar: '../../static/default-avatar.jpg',
                username: '张三',
                text: '期待！有什么特色小吃啊？',
                createTime: '2024-03-21 12:25'
              },
              {
                avatar: '../../static/default-avatar.jpg',
                username: '王五',
                text: '抽奖有什么奖品啊？',
                createTime: '2024-03-21 12:30'
              }
            ]
          },
          '4': {
            abstractContent: '求推荐好的自习室',
            detail: '最近图书馆人太多了，想找个安静的自习室，大家有推荐的吗？最好是靠近宿舍区的，有空调的。',
            images: [],
            avatar: '../../static/default-avatar.jpg',
            username: '王五',
            createTime: '2024-03-21 11:15',
            likeNum: 42,
            readNum: 89,
            liked: false,
            comments: [
              {
                avatar: '../../static/default-avatar.jpg',
                username: '李四',
                text: '教学楼B区3楼的自习室不错，人少安静',
                createTime: '2024-03-21 11:20'
              },
              {
                avatar: '../../static/default-avatar.jpg',
                username: '赵六',
                text: '宿舍楼下的自习室也可以，就是位置比较少',
                createTime: '2024-03-21 11:25'
              }
            ]
          },
          '5': {
            abstractContent: '二手教材交易',
            detail: '出售大二计算机专业教材，九成新，价格可议。包括：数据结构、操作系统、计算机网络等。有意者请联系。',
            images: [
              '../../static/banner1.jpg',
              '../../static/banner2.jpg',
              '../../static/banner3.jpg'
            ],
            avatar: '../../static/default-avatar.jpg',
            username: '赵六',
            createTime: '2024-03-21 10:30',
            likeNum: 23,
            readNum: 45,
            liked: false,
            comments: [
              {
                avatar: '../../static/default-avatar.jpg',
                username: '张三',
                text: '数据结构多少钱？',
                createTime: '2024-03-21 10:35'
              },
              {
                avatar: '../../static/default-avatar.jpg',
                username: '王五',
                text: '计算机网络还有吗？',
                createTime: '2024-03-21 10:40'
              }
            ]
          }
        }

        // 获取对应的测试数据
        console.log('获取对应的测试数据'+this.id)
        const data = mockData[this.id] || mockData['1']
        
        // 如果已经有post数据，则合并评论数据
        if (this.post) {
          data.comments = data.comments || []
        }
        
        // 更新页面数据
        this.detail = data
        this.comments = data.comments || []
        this.liked = data.liked || false
      }, 500) // 模拟500ms的网络延迟
    },
    likePost() {
      // 这里可以调用后端点赞接口
      this.liked = !this.liked
      this.detail.likeNum += this.liked ? 1 : -1
      // uni.request({ ... })
    },
    reportPost() {
      uni.showToast({ title: '已举报', icon: 'none' })
      // uni.request({ ... })
    },
    previewImage(idx) {
      uni.previewImage({
        urls: this.detail.images,
        current: idx
      })
    },
    goBack() {
      // 使用navigateBack返回，这样可以保持studentIndex页面的状态
      uni.navigateBack({
        delta: 1,
        success: () => {
          // 返回成功后的回调
          console.log('返回成功')
        },
        fail: (err) => {
          // 如果返回失败（比如没有上一页），则重定向到首页
          console.error('返回失败：', err)
          uni.redirectTo({
            url: '/pages/home/studentIndex'
          })
        }
      })
    }
  }
}
</script>

<style lang="scss">
.detail-container {
  background: #fff;
  min-height: 100vh;
  padding-bottom: 40rpx;

  .nav-bar {
    position: fixed;
    top: 0;
    left: 0;
    right: 0;
    z-index: 100;
    height: 88rpx;
    background: rgba(255, 255, 255, 0.98);
    backdrop-filter: blur(20px);
    display: flex;
    align-items: center;
    padding: 0 30rpx;
    box-shadow: 0 4rpx 20rpx rgba(0, 0, 0, 0.05);

    .back-btn {
      display: flex;
      align-items: center;
      gap: 8rpx;
      padding: 10rpx;
      
      text {
        font-size: 28rpx;
        color: #333;
      }
    }
  }

  .detail-header {
    margin-top: 88rpx;  // 为顶部导航栏留出空间
    display: flex;
    align-items: center;
    padding: 30rpx 20rpx 10rpx 20rpx;
    .avatar { width: 60rpx; height: 60rpx; border-radius: 50%; }
    .user-info { margin-left: 16rpx; flex: 1;
      .username { font-weight: bold; color: #333; }
      .time { color: #bbb; font-size: 24rpx; margin-left: 10rpx; }
    }
    .report-btn {
      font-size: 24rpx;
      color: #ff4d4f;
      background: none;
      border: none;
    }
  }
  .detail-title { font-size: 36rpx; font-weight: bold; margin: 20rpx 20rpx 10rpx 20rpx; }
  .detail-content { font-size: 30rpx; color: #444; margin: 0 20rpx 20rpx 20rpx; }
  .detail-images {
    display: flex; flex-wrap: wrap; gap: 12rpx; margin: 0 20rpx 20rpx 20rpx;
    .detail-image { width: 220rpx; height: 220rpx; border-radius: 12rpx; }
  }
  .detail-actions {
    display: flex; align-items: center; gap: 40rpx; margin: 0 20rpx 20rpx 20rpx;
    .action-item { display: flex; align-items: center; gap: 8rpx; color: #999; }
  }
  .comments-section {
    background: #f8f9fa; border-radius: 20rpx; margin: 0 20rpx; padding: 20rpx;
    .comment-item { display: flex; align-items: flex-start; margin-bottom: 20rpx;
      .comment-avatar { width: 48rpx; height: 48rpx; border-radius: 50%; margin-right: 12rpx; }
      .comment-content {
        .comment-username { font-weight: 500; color: #2196F3; margin-right: 8rpx; }
        .comment-text { color: #333; }
        .comment-time { color: #bbb; font-size: 22rpx; margin-left: 10rpx; }
      }
    }
  }
}
</style>