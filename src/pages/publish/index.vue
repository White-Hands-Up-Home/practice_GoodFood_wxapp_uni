<script setup>
import score from '@/components/star/star';
import { ref } from 'vue';
import { login_store } from '../../stores/login';
import { storeToRefs } from 'pinia';
import { onLoad } from '@dcloudio/uni-app';

const main = storeToRefs(login_store());
//图片路径
const tempFilePaths = ref([]);

//选择图片
const ChooseImg = () => {
  const that = this;
  uni.chooseImage({
    count: 3,
    // 最多3张图片
    sizeType: ['original', 'compressed'],
    // 指定是原图或压缩图
    sourceType: ['album', 'camera'],
    // 指定来源是相册或相机
    success: (res) => {
      uni.showLoading({
        title: '上传中...',
      });

      // 返回选定照片的本地文件路径列表，tempFilePath可以作为img标签的src属性显示图片
      tempFilePaths.value.push(res.tempFilePaths);
      console.log(tempFilePaths);
      uni.hideLoading();
    },
  });
};

//预览图片
const PreviewImg = (e) => {
  const index = e.target.dataset.index;
  uni.previewImage({
    current: tempFilePaths.value[index],
    urls: tempFilePaths.value[index],
  });
};

//删除图片
const DeleteImg = (e) => {
  const index = e.currentTarget.dataset.index; //获取当前长按图片下标
  uni.showModal({
    title: '提示',
    content: '确定要删除此图片吗？',
    success: function (res) {
      console.log(res);
      if (res.confirm) {
        tempFilePaths.value.splice(index, 1);
      } else if (res.cancel) {
        return false;
      }
    },
  });
};
//未登录跳转
onLoad(() => login_store().prevent());

//跳转到搜索页
const goToSearch = () => uni.navigateTo({ url: '../search/index' });
</script>

<template>
  <view style="height: 100%">
    <input class="input1" placeholder="添加店铺" @tap="goToSearch" />

    <!-- 图标和店铺名 -->
    <view class="head">
      <image class="location" src="/static/img/coordinates_fill.svg"></image>
      <text class="storeName" @tap="goToDetail">{{ main.comment_shop_name }}</text>
    </view>

    <!-- 星星组件 -->
    <view class="mb-5">
      <score></score>
    </view>

    <!-- 输入框 -->
    <textarea class="input2" placeholder="输入评价内容......"></textarea>

    <!-- 点击上传图片 -->
    <text class="word-class">点击上传图片，长按删除</text>
    <!-- 以下为图片选择 -->
    <view class="img_box">
      <view v-for="(item, index) in tempFilePaths" :key="index" class="imgs">
        <image :src="item" :data-index="index" mode="aspectFill" @longpress="DeleteImg" @tap="PreviewImg" />
      </view>
      <view class="imgs">
        <view v-if="tempFilePaths.length < 3" class="images" @tap="ChooseImg">
          <image src="/static/img/camera.svg" mode="widthFix" />
        </view>
      </view>
    </view>

    <!-- 上传按钮 -->
    <view class="UploadBtnarea">
      <button class="UploadBtnclass" style="width: 70%" @tap="UploadBtntap">
        <view>发布评价</view>
        <image src="/static/img/send(1).svg"></image>
      </button>
    </view>
  </view>
</template>

<style scoped>
.input1 {
  background-color: rgb(199, 196, 191);
  height: 80rpx;
  margin: 30rpx;
  margin-left: 7%;
  margin-right: 7%;
  border-radius: 50rpx;
  text-decoration-color: rgb(199, 196, 191);
  padding-left: 40rpx;
}

.location {
  height: 60rpx;
  width: 60rpx;
}

.storeName {
  color: rgb(32, 25, 19);
  font-size: large;
  font-weight: 800;
}

.head {
  display: flex;
  justify-content: flex-start;
  flex-direction: row;
  margin: 30rpx;
  margin-bottom: 0rpx;
}

.input2 {
  border: solid 1px grey;
  width: auto;
  height: 330rpx;
  padding-left: 40rpx;
  padding-right: 40rpx;
  padding-top: 10rpx;
  padding-bottom: 10rpx;
}

.word-class {
  font-size: 14px;
  color: rgb(95, 87, 87);
  margin-left: 10px;
}

/* 选择图片 */
.img_box {
  width: 690rpx;
  height: 340rpx;
  position: relative;
  display: flex;
  flex-wrap: wrap;
  margin: 0 auto;
  padding-top: 20px;
}

.imgs {
  width: 33.33333333%;
  display: flex;
  justify-content: center;
  margin-bottom: 20rpx;
}

.imgs image {
  width: 90%;
  max-height: 212rpx;
  border: 1px solid rgba(214, 212, 212, 0.1);
}

.imgs .images {
  position: relative;
}

.images button {
  width: 100%;
  height: 100%;
  position: absolute;
  top: 0;
  left: 0;
}

.img_box .images {
  width: 90%;
  height: 212rpx;
  border: 1px dashed #4b4b4b;
  border-radius: 30rpx;
  display: flex;
  align-items: center;
  justify-content: center;
}

.img_box .images > image {
  width: 60rpx;
  height: 60rpx;
}

/* 上传按钮 */
.UploadBtnarea {
  width: 100%;
  height: 50px;
  margin-top: 15px;
  margin-bottom: 15px;
}

.UploadBtnclass {
  height: 100%;
  background-color: rgb(255, 255, 255);
  align-items: center;
  box-shadow: 5rpx 7rpx 5rpx #aaa9a9e1;
  border-radius: 50rpx;
  display: flex;
  justify-content: center;
}

.UploadBtnclass view {
  font-size: 40rpx;
  color: rgb(253, 169, 12);
  width: 40%;
  padding: 4rpx;
}

.UploadBtnclass image {
  height: 60rpx;
  width: 60rpx;
}
</style>
