<template>
  <view class="container">
    <button class="diagnosis-button" @click="navigateTo('face')">面诊</button>
    <button class="diagnosis-button" @click="navigateTo('tongue')">舌诊</button>
    <button class="diagnosis-button" @click="navigateTo('ear')">耳诊</button>
	<tabBar ref="customTabBar" selectedIndex = 0 ></tabBar>
  </view>
</template>

<script>
import tabBar from "@/pages/tabBar/tabBar.vue"


export default {
   components:{
   	    tabBar //引用对应的组件
   },
  onLoad() {
	uni.hideTabBar()
  },	
  onShow() {
    // 更新tabBar的选中索引
    this.$refs.customTabBar.updateActiveTabIndex();
  },	 
  methods: {
    navigateTo(type) {
      // 根据传入的类型跳转到对应页面
      uni.navigateTo({
        url: `/pages/home/wangzhen/${type}`
      });
    }
  }
}
</script>


<style>
.container {
  display: flex;
  flex-direction: column;
  justify-content: space-around;
  align-items: center;
  height: 100%;
  padding: 20px;
  box-sizing: border-box;
  padding-bottom: 50px; /* 预留出tabBar的空间 */
}

.diagnosis-button {
  width: 80%; /* 按钮宽度 */
  padding: 15px 0; /* 垂直内边距 */
  font-size: 28px; /* 文字大小 */
  color: #fff; /* 文字颜色 */
  border: none; /* 去除边框 */
  border-radius: 25px; /* 边角圆滑 */
  background-color: #007AFF; /* 背景颜色 */
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1); /* 盒阴影效果 */
  margin-bottom: 30px; /* 按钮间的间隔 */
}

.diagnosis-button:last-child {
  margin-bottom: 0; /* 最后一个按钮无底部间隔 */
}

.diagnosis-button:active {
  background-color: #0056b3; /* 激活时背景颜色 */
}
</style>