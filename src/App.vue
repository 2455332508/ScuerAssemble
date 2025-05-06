<template>
  <view class="container">
    <view class="content">
      <!-- 页面内容将在这里显示 -->
    </view>
  </view>
</template>

<script setup>
import { onLaunch, onShow, onHide } from '@dcloudio/uni-app'

onLaunch(() => {
  console.log('App Launch')
})

onShow(() => {
  console.log('App Show')
})

onHide(() => {
  console.log('App Hide')
})
</script>

<style>
/*每个页面公共css */
.container {
  width: 100%;
  min-height: 100vh;
}

.content {
  width: 100%;
}
</style>