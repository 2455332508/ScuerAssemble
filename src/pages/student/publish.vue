<template>
  <view class="publish-container">
    <view class="header">
      <uni-icons type="back" size="24" @click="goBack" />
      <text class="title">帖子发布</text>
    </view>
    <view class="form-section">
      <view class="form-item">
        <text class="label">标题</text>
        <input class="input" v-model="form.abstractContent" maxlength="20" placeholder="输入标题" @input="onTitleInput" />
        <text class="input-tip" v-if="titleError">标题长度不能超过20个字符</text>
      </view>
      <view class="form-item">
        <text class="label">类目</text>
        <picker :range="categoryList" range-key="name" @change="onCategoryChange">
          <view class="picker-view">
            <text>{{ form.categoryId ? form.categoryId : '选择类目' }}</text>
            <uni-icons type="arrowdown" size="18" />
          </view>
        </picker>
        <view class="category-icons">
          <view v-for="cat in categoryList" :key="cat.value" class="cat-icon" :class="{active: form.categoryId === cat.value}" @click="selectCategory(cat)">
            <uni-icons :type="cat.icon" size="32" :color="form.categoryId === cat.value ? '#2196F3' : '#bbb'" />
            <text>{{ cat.name }}</text>
          </view>
        </view>
      </view>
      <view class="form-item">
        <text class="label">校区</text>
        <picker :range="campusList" @change="onCampusChange">
          <view class="picker-view">
            <text>{{ form.campus }}</text>
            <uni-icons type="arrowdown" size="18" />
          </view>
        </picker>
      </view>
      <view class="form-item">
        <text class="label">详情</text>
        <textarea class="textarea" v-model="form.detail" :maxlength="999" placeholder="点击输入您要说的内容" @input="onDetailInput" />
        <text class="input-tip" v-if="detailError">详情内容需在1~999字符</text>
      </view>
      <view class="form-item">
        <text class="label">添加图片</text>
        <view class="img-list">
          <view v-for="(img, idx) in form.images" :key="idx" class="img-preview">
            <image :src="img" mode="aspectFill" class="img-thumb" />
          </view>
          <view class="img-add" @click="chooseImage" v-if="form.images.length < 9">
            <uni-icons type="plusempty" size="36" color="#bbb" />
          </view>
        </view>
        <text class="img-tip">最多9张，单张图片小于2M</text>
      </view>
      <view class="form-item row">
        <text class="label">评论</text>
        <switch :checked="form.allowComment" @change="onAllowCommentChange" color="#2196F3" />
        <text class="switch-tip">{{ form.allowComment ? '允许评论' : '禁止评论' }}</text>
      </view>
      <view class="form-item row">
        <checkbox :checked="readNotice" @change="onReadNoticeChange" />
        <text class="notice-text">我已阅读并同意 <text class="notice-link" @click="goNotice">《发布须知》</text></text>
      </view>
      <button class="submit-btn" type="primary" @click="submit" >立即发布</button>
      <!-- <button class="submit-btn" type="primary" @click="submit" :disabled="!canSubmit">立即发布</button> -->
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      token: '',
      form: {
        abstractContent: '',// abstractContent
        categoryId: '', //categoryId
        detail: '',
        images: [],
        allowComment: 1,
        campus: '江安'
      },
      categoryList: [
        { name: '求助打听', value: 1, icon: 'help' },
        { name: '二手闲置', value: 2, icon: 'gift' },
        { name: '相亲交友', value: 3, icon: 'heart' },
        { name: '校园趣事', value: 4, icon: 'chat' }
      ],
      titleError: false,
      detailError: false,
      readNotice: false,
      campusList: ['江安', '望江', '华西']
    }
  },
  computed: {
    canSubmit() {

      console.log( this.form.abstractContent.length > 0 &&
        this.form.abstractContent.length <= 20);

        console.log(this.form.categoryId > 0);

        console.log( this.form.detail.length >= 1 && this.form.detail.length <= 999);
        
        console.log(this.readNotice );

        console.log( this.form.campus);

      return (
        this.form.abstractContent.length > 0 &&
        this.form.abstractContent.length <= 20 &&
        this.form.categoryId > 0 &&
        this.form.detail.length >= 1 &&
        this.form.detail.length <= 999 &&
        this.readNotice &&
        this.form.campus
      )
    }
  },
  onLoad() {
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
      return
    }
  },
  methods: {
    goBack() {
      uni.navigateBack({ delta: 1 })
    },
    onTitleInput(e) {
      this.titleError = this.form.abstractContent.length > 20
    },
    onCategoryChange(e) {
      const idx = e.detail.value
      this.form.categoryId = this.categoryList[idx].value
    },
    selectCategory(cat) {
      this.form.categoryId = cat.value
    },
    onDetailInput(e) {
      this.detailError = this.form.detail.length < 1 || this.form.detail.length > 999
    },
    chooseImage() {
      uni.chooseImage({
        count: 9 - this.form.images.length,
        sizeType: ['compressed'],
        success: (res) => {
          res.tempFilePaths.forEach(tempFilePath => {
            // H5环境
            if (process.env.UNI_PLATFORM === 'h5') {
              fetch(tempFilePath)
                .then(response => response.blob())
                .then(blob => {
                  const file = new File([blob], 'image.jpg', { type: blob.type });
                  const formData = new FormData();
                  formData.append('image', file);

                  const xhr = new XMLHttpRequest();
                  xhr.open('POST', 'http://localhost:8080/upload', true);
                  xhr.setRequestHeader('token', this.token);

                  xhr.onload = () => {
                    if (xhr.status === 200) {
                      try {
                        const data = JSON.parse(xhr.responseText);
                        if (data.code === 1 && data.data) {
                          this.form.images.push(data.data);
                          uni.showToast({ title: '图片上传成功', icon: 'success' });
                        } else {
                          uni.showToast({ title: data.msg || '图片上传失败', icon: 'none' });
                        }
                      } catch (error) {
                        uni.showToast({ title: '图片上传失败', icon: 'none' });
                      }
                    } else {
                      uni.showToast({ title: '图片上传失败', icon: 'none' });
                    }
                  };

                  xhr.onerror = () => {
                    uni.showToast({ title: '图片上传失败', icon: 'none' });
                  };

                  xhr.send(formData);
                })
                .catch(() => {
                  uni.showToast({ title: '处理文件失败', icon: 'none' });
                });
            } else {
              // 非H5环境
              uni.uploadFile({
                url: 'http://localhost:8080/upload',
                filePath: tempFilePath,
                name: 'file',
                header: { 'token': this.token },
                success: (uploadRes) => {
                  try {
                    const data = JSON.parse(uploadRes.data);
                    if (data.code === 1 && data.data) {
                      this.form.images.push(data.data);
                      uni.showToast({ title: '图片上传成功', icon: 'success' });
                    } else {
                      uni.showToast({ title: data.msg || '图片上传失败', icon: 'none' });
                    }
                  } catch {
                    uni.showToast({ title: '图片上传失败', icon: 'none' });
                  }
                },
                fail: () => {
                  uni.showToast({ title: '图片上传失败', icon: 'none' });
                }
              });
            }
          });
        },
        fail: () => {
          uni.showToast({ title: '选择图片失败', icon: 'none' });
        }
      });
    },
    goNotice() {
      uni.navigateTo({ url: '/pages/about/specification' })
    },
    onReadNoticeChange(e) {///
      this.readNotice = this.form.detail.length > 0;
    },
    onAllowCommentChange(e) {
      this.form.allowComment = e.detail.value;
    },
    onCampusChange(e) {
      this.form.campus = this.campusList[e.detail.value];
    },
    submit() {
      console.log(this.form);
      if (!this.canSubmit) {
        uni.showToast({ title: '请完善所有信息', icon: 'none' })
        return
      }
      
      // 发送发布请求
      uni.request({
        url: 'http://localhost:8080/posts/publish',
        method: 'POST',
        header: {
          'token': this.token
        },
        data: {
          abstractContent: this.form.abstractContent,
          categoryId: this.form.categoryId,
          detail: this.form.detail,
          images: this.form.images,
          allowComment: this.form.allowComment,
          campus: this.form.campus
        },
        success: (res) => {
          if (res.data.code === 1) {
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
            uni.showToast({
              title: '发布成功',
              icon: 'success'
            })
            // 发布成功后返回上一页
            setTimeout(() => {
              uni.navigateBack()
            }, 1500)
          } else {
            uni.showToast({
              title: res.data.msg || '发布失败',
              icon: 'none'
            })
          }
        },
        fail: () => {
          uni.showToast({
            title: '网络错误，请稍后重试',
            icon: 'none'
          })
        }
      })
    }
  }
}
</script>

<style scoped lang="scss">
.publish-container {
  background: #fff;
  min-height: 100vh;
  padding-bottom: 40rpx;
}
.header {
  display: flex;
  align-items: center;
  padding: 30rpx 30rpx 0 30rpx;
  background: #2196F3;
  color: #fff;
  .title {
    flex: 1;
    text-align: center;
    font-size: 36rpx;
    font-weight: 600;
    color: #fff;
  }
}
.form-section {
  padding: 30rpx;
}
.form-item {
  margin-bottom: 36rpx;
  .label {
    font-size: 30rpx;
    color: #333;
    margin-bottom: 12rpx;
    display: block;
  }
  .input, .textarea {
    width: 100%;
    border: 1px solid #eee;
    border-radius: 8rpx;
    padding: 18rpx;
    font-size: 30rpx;
    margin-top: 8rpx;
    background: #fafbfc;
  }
  .input-tip {
    color: #ff4d4f;
    font-size: 24rpx;
    margin-top: 6rpx;
  }
  .picker-view {
    display: flex;
    align-items: center;
    border: 1px solid #eee;
    border-radius: 8rpx;
    padding: 18rpx;
    font-size: 30rpx;
    background: #fafbfc;
    margin-top: 8rpx;
    width: 100%;
    justify-content: space-between;
  }
  .category-icons {
    display: flex;
    gap: 36rpx;
    margin-top: 18rpx;
    .cat-icon {
      display: flex;
      flex-direction: column;
      align-items: center;
      font-size: 26rpx;
      color: #888;
      cursor: pointer;
      &.active {
        color: #2196F3;
      }
    }
  }
  .img-list {
    display: flex;
    gap: 18rpx;
    flex-wrap: wrap;
    margin-top: 8rpx;
    .img-preview {
      position: relative;
      .img-thumb {
        width: 120rpx;
        height: 120rpx;
        border-radius: 8rpx;
        object-fit: cover;
        border: 1px solid #eee;
      }
    }
    .img-add {
      width: 120rpx;
      height: 120rpx;
      display: flex;
      align-items: center;
      justify-content: center;
      border: 1px dashed #bbb;
      border-radius: 8rpx;
      background: #fafbfc;
      cursor: pointer;
    }
  }
  .img-tip {
    color: #888;
    font-size: 24rpx;
    margin-top: 6rpx;
  }
  .switch-tip {
    margin-left: 18rpx;
    color: #888;
    font-size: 26rpx;
  }
  &.row {
    display: flex;
    align-items: center;
    gap: 18rpx;
  }
  .notice-text {
    color: #888;
    font-size: 26rpx;
    margin-left: 8rpx;
    .notice-link {
      color: #2196F3;
      text-decoration: underline;
    }
  }
}
.submit-btn {
  width: 100%;
  height: 88rpx;
  background: #2196F3;
  color: #fff;
  font-size: 32rpx;
  border-radius: 16rpx;
  margin-top: 30rpx;
  font-weight: 600;
}
</style>
