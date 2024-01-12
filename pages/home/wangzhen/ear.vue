<template>
  <view class="container">
    <view class="upload-container">
      <button class="upload-btn" @click="chooseAndUploadPhoto">上传照片</button>
    </view>

	<view v-if="uploadurl" class="text-section">
	  <text class="image-text">你的照片</text>
	</view>
    <view v-if="uploadurl" class="image-container">
      <image class="sample-image" :src="uploadurl" mode="aspectFit" @click="viewImage(uploadurl)"></image>
    </view>
	
	<view class="text-section">
	  <text class="image-text">示例照片</text>
	</view>
    <view class="image-container">
      <image class="sample-image" :src="imageSrc" mode="aspectFit" @click="viewImage(imageSrc)"></image>
    </view>
	
  </view>
</template>

<script>
export default {
	data() {
	    return {
	      // 如果是网络图片，请确保图片链接是可访问的
	      imageSrc: 'https://itcm-wyu.oss-cn-guangzhou.aliyuncs.com/example/ear/ear_example.jpg',
		  uploadurl:""
	    }
	  },
	  
  methods: {
    chooseAndUploadPhoto() {
      // 选择图片
      uni.chooseImage({
        count: 1, // 默认9，这里设置成一张
        sizeType: ['original', 'compressed'], // 可以指定是原图还是压缩图，默认二者都有
        sourceType: ['album', 'camera'], // 可以指定来源是相册还是相机，默认二者都有
        success: (res) => {
          const tempFilePaths = res.tempFilePaths;
		  const token = uni.getStorageSync('token');
          // 上传图片
          uni.uploadFile({
            url: 'http://121.41.80.48:9003/user-ear/upload', // 您的后端接口地址
            filePath: tempFilePaths[0],
            name: 'file', // 这里根据后端的需要进行修改
			formData: {
			       'token': token // 将token包含在formData中
			     },
            success: (uploadFileRes) => {
              uni.showToast({
                title: '上传成功，请勿重复提交',
                duration: 2000
              });
			  this.uploadurl = JSON.parse(uploadFileRes.data).url;
            },
            fail: (uploadFileErr) => {
              console.log(uploadFileErr); // 输出错误信息
              uni.showToast({
                title: '上传失败',
                icon: 'none',
                duration: 2000
              });
            }
          });
        }
      });
    },
    viewImage(src) {
      // 实现点击查看大图的逻辑
		  uni.previewImage({
		    urls: [src], // 预览的图片数组
		    current: src // 当前显示的图片
		  })
    }
  }
}
</script>

<style>
.container {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  height: 100%;
  padding: 20px;
  box-sizing: border-box;
}

.upload-container {
  margin-bottom: 20px;
}

.upload-btn {
  padding: 10px 100px;/* 增加水平内边距以使按钮更宽 */
  font-size: 16px;
  color: #fff;
  background-color: #1a73e8;
  border-radius: 5px;
  border: none;
  outline: none;
  cursor: pointer;
}

.upload-btn:active {
  background-color: #1669c7;
}

.text-section {
  margin-bottom: 10px;
}

.image-text {
  font-size: 18px;
  color: #333;
}

.image-container {
  width: 100%;
  padding: 10px;
  box-sizing: border-box;
}

.sample-image {
  width: 100%; /* 图片宽度占满容器 */
  border-radius: 10px;
  box-shadow: 2px 2px 10px rgba(0, 0, 0, 0.1);
}
</style>