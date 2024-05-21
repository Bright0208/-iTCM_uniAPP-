<template>
  <view class="container">
    <view class="upload-container">
      <button class="upload-btn" @click="chooseAndUploadPhoto">{{ uploadButtonText }}</button>
    </view>

	<view v-if="uploadurl" class="text-section">
	  <text class="image-text">你的照片</text>
	</view>
    <view v-if="uploadurl" class="image-container">
      <image class="sample-image" :src="uploadurl" mode="aspectFit" @click="viewImage(uploadurl)"></image>
    </view>
	<view class="text-section">
	  <button v-if="uploadurl" class="upload-btn" @click="earSubmit">提交</button>
	</view>
	<view class="text-section">
	  <text class="image-text">示例照片</text>
	</view>
    <view class="image-container">
      <image class="sample-image" :src="imageSrc" mode="aspectFit" @click="viewImage(imageSrc)"></image>
    </view>
	<!-- 加载中遮罩层 -->
		<view v-if="isLoading" class="overlay">
		  <view class="loading">
		    <text>提交中...</text>
			      <div class="spinner">
			        <div class="bounce1"></div>
			        <div class="bounce2"></div>
			        <div class="bounce3"></div>
			      </div>
		  </view>
		</view>
				<view>
					<!-- 提示信息弹窗 -->
					<uni-popup ref="message" type="message">
						<uni-popup-message :type="msgType" :message="messageText" :duration="4000"></uni-popup-message>
					</uni-popup>
				</view>
	</view>
  </view>
</template>

<script>
export default {
	data() {
	    return {
	      // 如果是网络图片，请确保图片链接是可访问的
	      imageSrc: 'https://itcm-wyu.oss-cn-guangzhou.aliyuncs.com/example/ear/ear_example.jpg',
		  uploadurl:"",
		  isLoading: false,
		  type: 'center',
		  msgType: 'success',
		  messageText: '这是一条成功提示'
	    }
	  },
  computed: {
    uploadButtonText() {
      return this.uploadurl ? '重新上传' : '上传照片';
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
		  this.uploadurl = tempFilePaths[0]
        }
      });
    },
	earSubmit(){
		this.isLoading = true;
		// 上传图片
		const token = uni.getStorageSync('token');
		const userInformation = uni.getStorageSync('userInformation');
		// 上传图片
		uni.uploadFile({
		  url: 'http://8.138.22.54:9001/user-ear/upload', // 您的后端接口地址192.168.1.161、、8.138.22.54
		  filePath: this.uploadurl,
		  name: 'file', // 这里根据后端的需要进行修改
					formData: {
					       'token': token ,// 将token包含在formData中
						   'userInformation':userInformation
					     },
		  success: (uploadFileRes) => {
			  		this.isLoading = false;
					  this.dialogToggle("success","已经提交舌诊，请提交手诊")
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
	},
    viewImage(src) {
      // 实现点击查看大图的逻辑
		  uni.previewImage({
		    urls: [src], // 预览的图片数组
		    current: src // 当前显示的图片
		  })
    },
	dialogToggle(type,text) {
		this.msgType = type
		this.messageText = text
		this.$refs.message.open()
	},
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
.overlay {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-color: rgba(0, 0, 0, 0.5); /* 半透明 */
  z-index: 999; /* 确保遮罩层在最上层 */
  display: flex;
  justify-content: center;
  align-items: center;
}
.loading {
  /* 加载动画样式 */
	color: white;
	font-size: 90rpx;
}
.spinner {
  margin: 100px auto;
  width: 50px;
  text-align: center;
}

.spinner > div {
  width: 12px;
  height: 12px;
  background-color: #333;
  border-radius: 100%;
  display: inline-block;
  -webkit-animation: bouncedelay 1.4s infinite ease-in-out both;
  animation: bouncedelay 1.4s infinite ease-in-out both;
}

.spinner .bounce1 {
  -webkit-animation-delay: -0.32s;
  animation-delay: -0.32s;
}

.spinner .bounce2 {
  -webkit-animation-delay: -0.16s;
  animation-delay: -0.16s;
}

@-webkit-keyframes bouncedelay {
  0%, 80%, 100% { -webkit-transform: scale(0) }
  40% { -webkit-transform: scale(1.0) }
}

@keyframes bouncedelay {
  0%, 80%, 100% {
    transform: scale(0);
    -webkit-transform: scale(0);
  } 40% {
    transform: scale(1.0);
    -webkit-transform: scale(1.0);
  }
} 
</style>