<template>
  <view class="map-container">
    <view id="container" class="map"></view>
    <view class="search-box">
      <input 
        type="text" 
        v-model="searchKeyword" 
        placeholder="搜索地址" 
        @confirm="handleSearch"
        class="search-input"
      />
    </view>
    <view class="poi-list" v-if="poiList.length > 0">
      <scroll-view scroll-y class="poi-scroll">
        <view 
          v-for="(poi, index) in poiList" 
          :key="index"
          class="poi-item"
          @click="handlePoiClick(poi)"
        >
          <text class="poi-name">{{ poi.name }}</text>
          <text class="poi-address">{{ poi.address }}</text>
          <text class="poi-distance" v-if="poi.distance">距离：{{ poi.distance }}米</text>
        </view>
      </scroll-view>
    </view>
    <view class="location-info" v-if="location">
      <text class="location-text">{{ location.address }}</text>
    </view>
  </view>
</template>

<script setup>
import { ref, onMounted, onUnmounted } from 'vue'
import AMapLoader from '@amap/amap-jsapi-loader'

const location = ref(null)
const searchKeyword = ref('')
const poiList = ref([])
let map = null
let marker = null
let circle = null
let geolocation = null
let autoRefresh = null
let infoWindow = null
let placeSearch = null

// 初始化地图
const initMap = async () => {
  try {
    const AMap = await AMapLoader.load({
      key: 'e531c3e9c4cd55302e6552b180cf7450',
      version: '2.0',
      plugins: [
        'AMap.Geolocation',
        'AMap.Geocoder',
        'AMap.PlaceSearch',
        'AMap.Scale',
        'AMap.ToolBar',
        'AMap.InfoWindow',
        'AMap.AutoComplete'
      ]
    })

    // 创建地图实例
    map = new AMap.Map('container', {
      zoom: 15,
      center: [116.397428, 39.90923],
      resizeEnable: true
    })

    // 添加地图控件
    map.addControl(new AMap.Scale())
    map.addControl(new AMap.ToolBar({
      position: 'RB'
    }))

    // 创建定位实例
    geolocation = new AMap.Geolocation({
      enableHighAccuracy: true,
      timeout: 10000,
      buttonPosition: 'RB',
      buttonOffset: new AMap.Pixel(10, 20),
      zoomToAccuracy: true,
      GeoLocationFirst: true,
      needAddress: true,
      convert: true
    })

    // 创建信息窗体
    infoWindow = new AMap.InfoWindow({
      isCustom: true,
      autoMove: true,
      offset: new AMap.Pixel(0, -30)
    })

    // 将定位控件添加到地图
    map.addControl(geolocation)

    // 获取当前位置
    getCurrentPosition()

    // 设置自动刷新定位
    autoRefresh = setInterval(() => {
      getCurrentPosition()
    }, 30000) // 每30秒刷新一次

  } catch (error) {
    console.error('地图加载失败：', error)
    uni.showToast({
      title: '地图加载失败',
      icon: 'none'
    })
  }
}

// 获取当前位置
const getCurrentPosition = () => {
  if (!geolocation) return

  geolocation.getCurrentPosition((status, result) => {
    if (status === 'complete') {
      const { position, accuracy, formattedAddress } = result
      
      // 更新位置信息
      location.value = {
        longitude: position.lng,
        latitude: position.lat,
        // address: formattedAddress,
        address: '川大江安',
        accuracy: accuracy
      }

      console.log(location.address)

      // 更新标记点
      updateMarker(position)

      // 更新精度圈
      updateCircle(position, accuracy)

      // 设置地图中心点
      map.setCenter([position.lng, position.lat])

      // 搜索周边POI
      searchNearbyPOI(position)
    } else {
      console.error('定位失败：', result.message)
      uni.showToast({
        title: '定位失败，请检查定位权限',
        icon: 'none'
      })
    }
  })
}

// 更新标记点
const updateMarker = (position) => {
  if (marker) {
    marker.setPosition(position)
  } else {
    marker = new AMap.Marker({
      position: position,
      map: map,
      icon: 'https://webapi.amap.com/theme/v1.3/markers/n/mark_b.png',
      offset: new AMap.Pixel(-13, -30)
    })

    // 点击标记点显示信息窗体
    marker.on('click', () => {
      showInfoWindow(position)
    })
  }
}

// 显示信息窗体
const showInfoWindow = (position) => {
  const content = `
    <div style="padding: 10px;">
      <h4 style="margin: 0 0 5px 0;">当前位置</h4>
      <p style="margin: 0;">${location.value.address}</p>
    </div>
  `
  infoWindow.setContent(content)
  infoWindow.open(map, position)
}

// 更新精度圈
const updateCircle = (position, accuracy) => {
  if (circle) {
    circle.setCenter(position)
    circle.setRadius(accuracy)
  } else {
    circle = new AMap.Circle({
      center: position,
      radius: accuracy,
      fillColor: '#1791fc',
      fillOpacity: 0.2,
      strokeColor: '#1791fc',
      strokeWeight: 1,
      map: map
    })
  }
}

// 搜索周边POI
const searchNearbyPOI = (position) => {
  if (!placeSearch) {
    placeSearch = new AMap.PlaceSearch({
      pageSize: 10,
      pageIndex: 1,
      radius: 1000,
      extensions: 'all'
    })
  }

  placeSearch.searchNearBy('', position, 1000, (status, result) => {
    if (status === 'complete' && result.poiList) {
      poiList.value = result.poiList.pois.map(poi => ({
        name: poi.name,
        address: poi.address,
        location: poi.location,
        distance: poi.distance
      }))
    }
  })
}

// 处理POI点击
const handlePoiClick = (poi) => {
  const position = new AMap.LngLat(poi.location.lng, poi.location.lat)
  map.setCenter(position)
  
  // 添加POI标记
  const poiMarker = new AMap.Marker({
    position: position,
    map: map,
    icon: 'https://webapi.amap.com/theme/v1.3/markers/n/mark_r.png'
  })

  // 显示POI信息窗体
  const content = `
    <div style="padding: 10px;">
      <h4 style="margin: 0 0 5px 0;">${poi.name}</h4>
      <p style="margin: 0;">${poi.address}</p>
      <p style="margin: 5px 0 0 0; color: #666;">距离：${poi.distance}米</p>
    </div>
  `
  infoWindow.setContent(content)
  infoWindow.open(map, position)
}

// 搜索地址
const handleSearch = () => {
  if (!searchKeyword.value) return

  if (!placeSearch) {
    placeSearch = new AMap.PlaceSearch({
      pageSize: 10,
      pageIndex: 1,
      extensions: 'all',
      city: '全国'
    })
  }

  // 使用地理编码服务进行搜索
  const geocoder = new AMap.Geocoder({
    city: '全国'
  })

  geocoder.getLocation(searchKeyword.value, (status, result) => {
    if (status === 'complete' && result.geocodes.length) {
      const geocode = result.geocodes[0]
      const position = new AMap.LngLat(geocode.location.lng, geocode.location.lat)
      
      // 更新地图中心点和标记
      map.setCenter(position)
      updateMarker(position)
      
      // 更新位置信息
      location.value = {
        longitude: geocode.location.lng,
        latitude: geocode.location.lat,
        address: geocode.formattedAddress,
        accuracy: 0
      }

      // 搜索周边POI
      searchNearbyPOI(position)
    } else {
      // 如果地理编码失败，尝试使用POI搜索
      placeSearch.search(searchKeyword.value, (status, result) => {
        if (status === 'complete' && result.poiList.pois.length > 0) {
          poiList.value = result.poiList.pois.map(poi => ({
            name: poi.name,
            address: poi.address,
            location: poi.location,
            distance: poi.distance
          }))
          
          const poi = result.poiList.pois[0]
          const position = new AMap.LngLat(poi.location.lng, poi.location.lat)
          
          // 更新地图中心点和标记
          map.setCenter(position)
          updateMarker(position)
          
          // 更新位置信息
          location.value = {
            longitude: poi.location.lng,
            latitude: poi.location.lat,
            address: poi.address,
            accuracy: 0
          }
        } else {
          uni.showToast({
            title: '未找到相关地址',
            icon: 'none'
          })
        }
      })
    }
  })
}

onMounted(() => {
  initMap()
})

onUnmounted(() => {
  if (autoRefresh) {
    clearInterval(autoRefresh)
  }
  if (map) {
    map.destroy()
  }
})
</script>

<style scoped>
.map-container {  
  width: 100%;
  height: 100vh;
  position: relative;
  padding-top: 44px;
}

.map {
  width: 100%;
  height: 99%;
}

.search-box {
  position: fixed;
  top: 54px;
  left: 20px;
  right: 40px;
  z-index: 1000;
}

.search-input {
  width: 100%;
  height: 40px;
  background: rgba(255, 255, 255, 0.95);
  border: none;
  border-radius: 20px;
  padding: 0 20px;
  font-size: 14px;
  box-shadow: 0 2px 6px rgba(0, 0, 0, 0.1);
}

.poi-list {
  position: fixed;
  top: 104px;
  left: 20px;
  right: 20px;
  max-height: calc(100vh - 200px);
  background: rgba(255, 255, 255, 0.95);
  border-radius: 8px;
  box-shadow: 0 2px 6px rgba(0, 0, 0, 0.1);
  z-index: 1000;
}

.poi-scroll {
  max-height: calc(100vh - 200px);
}

.poi-item {
  padding: 12px 15px;
  border-bottom: 1px solid #eee;
  background: #fff;
}

.poi-item:last-child {
  border-bottom: none;
}

.poi-item:active {
  background: #f5f5f5;
}

.poi-name {
  font-size: 14px;
  color: #333;
  font-weight: 500;
  display: block;
  margin-bottom: 4px;
}

.poi-address {
  font-size: 12px;
  color: #666;
  display: block;
  margin-bottom: 4px;
}

.poi-distance {
  font-size: 12px;
  color: #999;
  display: block;
}

.location-info {
  position: fixed;
  bottom: 20px;
  left: 20px;
  max-width: 80%;
  background: rgba(255, 255, 255, 0.95);
  padding: 8px 12px;
  border-radius: 4px;
  box-shadow: 0 2px 6px rgba(0, 0, 0, 0.1);
  z-index: 1000;
}

.location-text {
  font-size: 12px;
  color: #666;
  display: block;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}
</style>
  