<template>
  <view class="page-container">
    <!-- 头部区域 -->
    <view class="header-section">
      <!-- 头像 -->
      <!-- <image class="user-avatar" :src="avatarUrl" ></image> -->
      <!-- 昵称和箭头 -->
      <view class="nickname-and-arrow" @click="navigateToEditProfile">
        <text class="nickname-text">账号：{{ mobile }}</text>
        <text class="arrow-icon">></text>
      </view>
    </view>
    <!-- 问诊结果展示区域 -->
   <view class="inquiry-section" >
      <view class="inquiry-title">诊断结果和建议</view>
      <view class="inquiry-content">
         <navigator v-if="isfinsh" url="/pages/user/resultAndSuggestion">点击查看</navigator>
		 <navigator v-else style="opacity: 0.5;   pointer-events: none;">请完成诊断之后再来查看</navigator>
      </view>
    </view>
	
	<view class="inquiry-section" >
	   <view class="inquiry-title">提交西医诊断报告</view>
	   <view class="inquiry-content">
	      <button @click="chooseAndUploadPhoto">点击提交</button>
	   </view>
	 </view>
	
	<view class="diagnosis-section">
	<view class="diagnosis-title">提交记录</view>
	<view class="diagnosis-category">
		<view class="category-title">望诊</view>
		<view class="diagnosis-result">
		<view class="result-item" @click="navigateToFaceDiagnosis">
			<text class="item-title">面  诊</text>
			<text class="arrow-icon">></text>
		</view>
		<view class="result-item" @click="navigateToTongueDiagnosis">
			<text class="item-title">舌  诊</text>
			<text class="arrow-icon">></text>
		</view>
		<view class="result-item" @click="navigateToEarDiagnosis">
			<text class="item-title">耳  诊</text>
			<text class="arrow-icon">></text>
		</view>
		<view class="result-item" @click="navigateToHandDiagnosis">
			<text class="item-title">手  诊</text>
			<text class="arrow-icon">></text>
		</view>
		</view>
	</view>
	<view class="diagnosis-category">
		<view class="category-title">问诊</view>
		<!-- 在这里添加问诊相关的结果项 -->
		<view class="result-item" @click="navigateToPhysiqueDiagnosis">
			<text class="item-title">体 质</text>
			<text class="arrow-icon">></text>
		</view>
		<view class="result-item" @click="navigateToRhinitisDiagnosis">
			<text class="item-title">鼻 炎</text>
			<text class="arrow-icon">></text>
		</view>
	</view>
	<view class="diagnosis-category">
		<view class="category-title">闻诊</view>
		<!-- 在这里添加闻诊相关的结果项 -->
		<view class="result-item" @click="navigateToVoiceDiagnosis">
			<text class="item-title">录 音</text>
			<text class="arrow-icon">></text>
		</view>
	</view>
	<view class="diagnosis-category">
		<view class="category-title">西医</view>
		<!-- 在这里添加闻诊相关的结果项 -->
		<view class="result-item" @click="navigateToWesternMedicine">
			<text class="item-title">西 医 报 告</text>
			<text class="arrow-icon">></text>
		</view>
	</view>
	</view>


	<view class="inquiry-section" style="margin-bottom: 120PX;">
	 <button @click="logout">退出登录</button>
	</view>
	
	<tabBar ref="customTabBar" selectedIndex = 2 ></tabBar>
  </view>
</template>
<script>
import tabBar from "@/pages/tabBar/tabBar.vue"
export default {
   components:{
   	    tabBar //引用对应的组件
   },
  onShow() {
    // 更新tabBar的选中索引
      this.$nextTick(() => {
        if (this.$refs.customTabBar) {
          // 更新tabBar的选中索引
          this.$refs.customTabBar.updateActiveTabIndex();
        }
      });
	// this.getPersonalResult();
	this.isFinsh()
  },
  data() {
    return {
      // 页面的数据
	  avatarUrl:"",
	  nickname:'',
	  mobile:'',
	  isfinsh:false
    };
  },
  
  onLoad() {
	  this.getAvatarAndNickname();
  },


  methods: {
    // 页面的方法
	navigateToEditProfile() {
	      // 导航到修改个人信息页面的逻辑
	      uni.navigateTo({
	        url: '/pages/user/updatePersonalInfo' // 修改为真实的页面路径
	      });
	    },
		
	// 导航到面诊页面的方法
	navigateToFaceDiagnosis() {
	  uni.navigateTo({
	    url: '/pages/user_record/wangzhen/face' // 修改为真实的面诊页面路径
	  });
	},
	// 导航到舌诊页面的方法
	navigateToTongueDiagnosis() {
	  uni.navigateTo({
	    url: '/pages/user_record/wangzhen/tongue' // 修改为真实的舌诊页面路径
	  });
	},
	// 导航到耳诊页面的方法
	navigateToEarDiagnosis() {
	  uni.navigateTo({
	    url: '/pages/user_record/wangzhen/ear' // 修改为真实的耳诊页面路径
	  });
	},				  	
	// 导航到手诊页面的方法
	navigateToHandDiagnosis(){
		uni.navigateTo({
		url: '/pages/user_record/wangzhen/hand' // 修改为真实的手诊页面路径
		});	
	},	
	// 导航到问诊体质页面的方法
	navigateToPhysiqueDiagnosis(){
		uni.navigateTo({
		url: '/pages/user_record/wenzhen/physique' // 修改为真实的体质页面路径
		});	
	},  
	// 导航到问诊鼻炎页面的方法
	navigateToRhinitisDiagnosis(){
		uni.navigateTo({
		url: '/pages/user_record/wenzhen/rhinitis' // 修改为真实的鼻炎页面路径
		});	
	},	
	// 导航到录音页面的方法
	navigateToVoiceDiagnosis(){
		uni.navigateTo({
		url: '/pages/user_record/wen2zhen/voice' // 修改为真实的闻诊页面路径
		});	
	},	
	// 导航到西医报告的方法
	navigateToWesternMedicine(){
		uni.navigateTo({
		url: '/pages/user_record/westernMedicine/westernMedicine' // 修改为真实的西医报告页面路径
		});	
	},	
	//获取个人头像和昵称
	getAvatarAndNickname(){
		uni.request({
		    url: 'http://8.138.22.54:9001/user/getAvatarAndNickname',
		    method: 'GET',
		    data: {
		        token: uni.getStorageSync('token')
		    }
		})
		.then((response) => {
			console.log(response.data.user);
		    // 请求成功的处理
			this.avatarUrl = response.data.user.avatarUrl;
			this.nickname = response.data.user.nickname;
			this.mobile = response.data.user.mobile;
			
		})
		.catch((error) => {
		    // 请求失败的处理
		    console.error(error);
		});
	},
	
	//获取诊断结果
	getPersonalResult(){
		uni.request({
		    url: 'http://8.138.22.54:9001/personalResults/get',
		    method: 'GET',
		    data: {
		        token: uni.getStorageSync('token')
		    }
		})
		.then((response) => {
		    // 请求成功的处理
		    console.log(response.data[0]);
			this.wenzhenResult = response.data[0].wenzhenResult;
			this.earDiagnosticResultsRemarks = response.data[0].earDiagnosticResultsRemarks;
			this.faceDiagnosticResultsRemarks = response.data[0].faceDiagnosticResultsRemarks;
			this.tongueFaceDiagnosticResultsRemarks = response.data[0].tongueFaceDiagnosticResultsRemarks;
		})
		.catch((error) => {
		    // 请求失败的处理
		    console.error(error);
		});
	},	 
	 //退出登录
	logout() {
	      uni.removeStorageSync('token'); // 删除所有存储在 Storage 中的内容
		  uni.removeStorageSync('identify'); // 删除所有存储在 Storage 中的内容
	      uni.reLaunch({
	        url: '/pages/user/login' // 返回到登录界面
	      });
	    },
	
	chooseAndUploadPhoto() {
	  // 选择图片
	  uni.chooseImage({
	    count: 1, // 默认9，这里设置成一张
	    sizeType: ['original', 'compressed'], // 可以指定是原图还是压缩图，默认二者都有
	    sourceType: ['album', 'camera'], // 可以指定来源是相册还是相机，默认二者都有
	    success: (res) => {
	      const tempFilePaths = res.tempFilePaths;
		  this.uploadurl = tempFilePaths[0]
		  
		  
		  const token = uni.getStorageSync('token');
		  const userInformation = uni.getStorageSync('userInformation');
		  uni.uploadFile({
		    url: 'http://8.138.22.54:9001/user-western-medicine/upload', // 您的后端接口地址8.138.22.54,,192.168.1.163
		    filePath: this.uploadurl,
		    name: 'file', // 这里根据后端的需要进行修改
		    formData: {
				'token': token ,// 将token包含在formData中
		    	'userInformation':userInformation
		    },
		    success: (uploadFileRes) => {
		  		if(uploadFileRes.statusCode >= 200 && uploadFileRes.statusCode < 300){
		  			uni.showToast({
		  			  title: '上传成功',
		  			  icon: 'none',
		  			  duration: 2000
		  			});
		  		}else{
		  			uni.showToast({
		  			  title: '上传失败',
		  			  icon: 'none',
		  			  duration: 2000
		  			});
		  		}
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
	isFinsh(){
      const hasWenzhen = !!uni.getStorageSync('wenzhen');
      const hasTongue = !!uni.getStorageSync('tongue');
      const hasFace = !!uni.getStorageSync('face');
      this.isfinsh = hasWenzhen && hasTongue && hasFace;
	}	  
  }
};
</script>

<style>
/* 页面容器 */
.page-container {
  padding: 20rpx;
  background-color: #f8f8f8;
}

/* 头部区域样式 */
.header-section {
  display: flex;
  align-items: center;
  background-color: #ffffff;
  padding: 20rpx;
  margin-bottom: 20rpx;
}

.user-avatar {
  width: 80rpx;
  height: 80rpx;
  border-radius: 50%; /* 圆形头像 */
  margin-right: 20rpx;
}

.nickname-and-arrow {
  display: flex;
  align-items: center;
  flex: 1;
}

.nickname-text {
  font-size: 32rpx;
  color: #333;
  flex: 1; /* 昵称占据剩余空间 */
}

.arrow-icon {
  font-size: 24rpx;
  color: #c0c0c0;
  padding: 10rpx; /* 箭头周围的空间 */
}

/* 望诊结果区域样式 */
.diagnosis-section {
  margin: 8px;
  padding: 20px;
  border: 1px solid #ccc;
  border-radius: 8px;
}

.diagnosis-title {
  font-size: 24px;
  font-weight: bold;
  margin-bottom: 15px;
}

.diagnosis-category {
  margin-bottom: 20px;
}

.category-title {
  font-size: 18px;
  font-weight: bold;
  margin-bottom: 10px;
}

.diagnosis-result {
  display: flex;
  flex-direction: column;
}

.result-item {
  display: flex;
  align-items: center;
  margin-bottom: 10px;
  padding: 10px;
  background-color: #f2f2f2;
  border-radius: 4px;
  cursor: pointer;
}

.result-item:last-child {
  margin-bottom: 0;
}

.item-title {
  flex-grow: 1;
  margin-right: 10px;
  font-size: 16px;
}

.arrow-icon {
  font-size: 18px;
  color: #007aff;
}

/* 响应式调整：如果屏幕宽度小于600px，减少左右边距 */
@media (max-width: 600px) {
  .diagnosis-section {
    margin: 10px;
    padding: 10px;
  }
}

/* 问诊结果区域样式 */
.inquiry-section {
  margin: 8px;
  padding: 20px;
  border: 1px solid #ccc;
  border-radius: 8px;	
  background-color: #ffffff;
}

.inquiry-title {
  font-size: 38rpx;
  color: #333;
  margin-bottom: 10rpx;
}

.inquiry-content {
  font-size: 34rpx;
  color: #666;
}
</style>