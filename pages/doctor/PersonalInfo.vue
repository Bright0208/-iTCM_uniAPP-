<template>
  <view class="page-container">
    <!-- 头部区域 -->
    <view class="header-section">
      <!-- 头像 -->
      <image class="user-avatar" :src="avatarUrl" ></image>
      <!-- 昵称和箭头 -->
      <view class="nickname-and-arrow" @click="navigateToEditProfile">
        <text class="nickname-text">{{ nickname }}</text>
        <text class="arrow-icon">></text>
      </view>
    </view>

    <!-- 标注记录展示区域 -->
    <view class="diagnosis-section">
      <view class="diagnosis-title">标注记录</view>
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
      </view>
    </view>
    
    <tabBar ref="customTabBar" selectedIndex = 1 ></tabBar>
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
    this.$refs.customTabBar.updateActiveTabIndex();
  },	
  data() {
    return {
      // 页面的数据
	  avatarUrl:"",
	  nickname:'',
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
        url: '/pages/doctor/wangzhen_revise/face' // 修改为真实的面诊页面路径
      });
    },
			  
    // 导航到舌诊页面的方法
    navigateToTongueDiagnosis() {
      uni.navigateTo({
        url: '/pages/doctor/wangzhen_revise/tongue' // 修改为真实的舌诊页面路径
      });
    },
				  
    // 导航到耳诊页面的方法
    navigateToEarDiagnosis() {
      uni.navigateTo({
        url: '/pages/doctor/wangzhen_revise/ear' // 修改为真实的耳诊页面路径
      });
    },				  
	//获取个人头像和昵称
	getAvatarAndNickname(){
		uni.request({
		    url: 'http://localhost:9001/user/getAvatarAndNickname',
		    method: 'GET',
		    data: {
		        token: uni.getStorageSync('token')
		    }
		})
		.then((response) => {
			console.log(response.data.user);
		    // 请求成功的处理
			this.avatarUrl = response.data.user.avatarUrl;
			this.nickname = response.data.user.nickName;
		})
		.catch((error) => {
		    // 请求失败的处理
		    console.error(error);
		});
	},
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

/* 调整箭头样式，使其可重用 */
.arrow-icon {
  font-size: 24rpx;
  color: #c0c0c0;
  padding: 10rpx; /* 箭头周围的空间 */
  margin-left: auto; /* 使箭头靠右显示 */
}

/* 望诊结果区域样式 */
.diagnosis-section {
  background-color: #ffffff;
  padding: 20rpx;
  margin-bottom: 30rpx;
}

.diagnosis-title {
  font-size: 38rpx;
  color: #333;
  margin-bottom: 10rpx;
}

.result-item {
  display: flex;
  align-items: center;
  margin-bottom: 10rpx;
}

.item-title {
  font-size: 34rpx;
  color: #666;
  min-width: 100rpx;
}

.item-desc {
  font-size: 34rpx;
  color: #666;
  flex: 1;
}

/* 问诊结果区域样式 */
.inquiry-section {
  background-color: #ffffff;
  padding: 20rpx;
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