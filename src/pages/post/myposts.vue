<template>
  <view class="mypost-bg">
    <view class="mypost-header">
      <view class="back-btn" @click="goBack">
        <uni-icons type="back" size="24" color="#333" />
        <text>返回</text>
      </view>
      <text class="title">我的帖子</text>
    </view>
    <view class="search-bar">
      <input class="search-input" placeholder="搜索标题、编号、正文内容" v-model="searchText" @input="filterPosts"/>
    </view>
    <view class="post-status-tabs">
      <view :class="['tab', activeTab === 'normal' ? 'active' : '']" @click="activeTab = 'normal'">已发送</view>
      <view :class="['tab', activeTab === 'deleted' ? 'active' : '']" @click="activeTab = 'deleted'">已删除</view>
    </view>
    <view class="post-list">
      <view v-for="post in filteredPosts" :key="post.id" class="post-item">
        <view class="post-meta">
          <text class="post-time">{{ post.createTime }}</text>
          <text class="post-status" :class="post.delFlag === 1 ? 'deleted' : 'normal'">
            {{ post.delFlag === 1 ? '已删除' : '正常' }}
          </text>
        </view>
        <view class="post-title">{{ post.abstractContent || post.title }}</view>
        <view class="post-content">{{ post.content }}</view>
      </view>
      <view v-if="filteredPosts.length === 0" class="empty-tip">暂无相关帖子</view>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      posts: [],
      searchText: '',
      activeTab: 'normal', // 'normal' or 'deleted'
    }
  },
  computed: {
    filteredPosts() {
      // 先按tab分类，再按搜索过滤
      let list = this.posts.filter(post => 
        this.activeTab === 'normal' ? post.delFlag !== 1 : post.delFlag === 1
      );
      if (this.searchText.trim()) {
        const key = this.searchText.trim().toLowerCase();
        list = list.filter(post =>
          (post.abstractContent || post.title || '').toLowerCase().includes(key) ||
          (post.content || '').toLowerCase().includes(key)
        );
      }
      return list;
    }
  },
  onLoad() {
    this.fetchMyPosts();
  },
  methods: {
    goBack() {
      uni.navigateBack({ delta: 1 });
    },
    fetchMyPosts() {
      const token = uni.getStorageSync('token');
      uni.request({
        url: 'http://localhost:8080/student/myposts',
        method: 'GET',
        header: { 'token': token },
        success: (res) => {
          if (res.data && res.data.data) {
            this.posts = res.data.data;
          } else {
            uni.showToast({ title: '获取帖子失败', icon: 'none' });
          }
        },
        fail: () => {
          uni.showToast({ title: '网络错误', icon: 'none' });
        }
      });
    },
    filterPosts() {
      // 触发computed刷新
      this.$forceUpdate();
    }
  }
}
</script>

<style scoped>
.mypost-bg {
  min-height: 100vh;
  background: #f7f8fa;
  padding-bottom: 40rpx;
}
.mypost-header {
  display: flex;
  align-items: center;
  padding: 24rpx 16rpx 0 16rpx;
  background: #fff;
  position: sticky;
  top: 0;
  z-index: 10;
  box-shadow: 0 2rpx 8rpx rgba(60,60,120,0.03);
}
.back-btn {
  display: flex;
  align-items: center;
  margin-right: 16rpx;
  cursor: pointer;
}
.title {
  font-size: 36rpx;
  font-weight: bold;
  color: #222;
  letter-spacing: 2rpx;
}
.search-bar {
  margin: 20rpx 16rpx 0 16rpx;
  background: #fff;
  border-radius: 32rpx;
  box-shadow: 0 2rpx 8rpx rgba(60,60,120,0.06);
  padding: 8rpx 24rpx;
  display: flex;
  align-items: center;
}
.search-input {
  width: 100%;
  border: none;
  background: transparent;
  font-size: 28rpx;
  outline: none;
  color: #333;
}
.post-status-tabs {
  display: flex;
  margin: 24rpx 16rpx 0 16rpx;
  background: #f0f1f5;
  border-radius: 20rpx;
  padding: 4rpx;
}
.tab {
  flex: 1;
  text-align: center;
  font-size: 28rpx;
  color: #888;
  padding: 16rpx 0;
  border-radius: 16rpx;
  cursor: pointer;
  transition: background 0.2s, color 0.2s;
}
.tab.active {
  color: #fff;
  background: #4f46e5;
  font-weight: bold;
  box-shadow: 0 2rpx 8rpx rgba(79,70,229,0.08);
}
.post-list {
  margin: 0 8rpx;
  margin-top: 16rpx;
}
.post-item {
  background: #fff;
  border-radius: 18rpx;
  box-shadow: 0 2rpx 12rpx rgba(60,60,120,0.06);
  padding: 20rpx 24rpx 16rpx 24rpx;
  margin-bottom: 18rpx;
  position: relative;
  transition: box-shadow 0.2s;
}
.post-item:hover {
  box-shadow: 0 4rpx 20rpx rgba(79,70,229,0.10);
}
.post-meta {
  display: flex;
  align-items: center;
  font-size: 22rpx;
  color: #bbb;
  margin-bottom: 8rpx;
}
.post-time {
  margin-right: 16rpx;
  color: #aaa;
}
.post-status {
  font-size: 22rpx;
  margin-left: 8rpx;
  padding: 2rpx 12rpx;
  border-radius: 12rpx;
  background: #f3f4f6;
}
.post-status.normal {
  color: #4f46e5;
  background: #eef2ff;
}
.post-status.deleted {
  color: #bbb;
  background: #f3f4f6;
}
.post-title {
  font-size: 30rpx;
  font-weight: bold;
  color: #222;
  margin-bottom: 6rpx;
  line-height: 1.3;
  word-break: break-all;
}
.post-content {
  font-size: 26rpx;
  color: #444;
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
  line-height: 1.5;
}
.empty-tip {
  text-align: center;
  color: #bbb;
  margin-top: 80rpx;
  font-size: 28rpx;
  letter-spacing: 2rpx;
}
</style>
