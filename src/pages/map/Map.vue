<template>
    
    <br>
    <br>

  <view class="map-page">
    <view class="header">
      <view class="back-button" @click="goBack">
        <uni-icons type="back" size="24" color="#333"></uni-icons>
      </view>
      <text class="title">地图定位</text>
    </view>
    <AMapLocation />
  </view>
</template>

<script setup>
import AMapLocation from '@/components/AMapLocation.vue'

const goBack = () => {
  // uni.redirectTo({
  //   url: '/pages/home/visitorIndex'
  uni.navigateBack({
    delta: 1
  })
}
</script>

<style scoped>
.map-page {
  width: 100%;
  height: 100vh;
  position: relative;
  background-color: #f5f5f5;
}

.header {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  height: 44px;
  background: rgba(255, 255, 255, 0.95);
  display: flex;
  align-items: center;
  padding: 0 15px;
  z-index: 1000;
  box-shadow: 0 2px 6px rgba(0, 0, 0, 0.1);
}

.back-button {
  width: 44px;
  height: 44px;
  display: flex;
  align-items: center;
  justify-content: center;
}

.title {
  flex: 1;
  text-align: center;
  font-size: 16px;
  font-weight: 500;
  color: #333;
  margin-right: 44px;
}
</style>
  