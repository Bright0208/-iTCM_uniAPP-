<template>
  <view class="container">  
     <uni-segmented-control :current="current" :values="items" @clickItem="onClickItem" styleType="button" activeColor="#4cd964"></uni-segmented-control>
     <view class="content">
         <view v-show="current === 0">
			 <rhinitis></rhinitis>
         </view>
         <view v-show="current === 1">
			 <physique></physique>
         </view>
     </view>
	<tabBar ref="customTabBar" selectedIndex=1 ></tabBar>
  </view>
</template>
<script>
import tabBar from "@/pages/tabBar/tabBar.vue"
import rhinitis from "./rhinitis.vue"
import physique from "./physique.vue"
export default {
   components:{
   	    tabBar, //引用对应的组件
		rhinitis,
		physique
   },
  onShow() {
    // 更新tabBar的选中索引
      this.$nextTick(() => {
        if (this.$refs.customTabBar) {
          // 更新tabBar的选中索引
          this.$refs.customTabBar.updateActiveTabIndex();
        }
      });
  },	   
  data() {
    return {
        items: ['鼻炎', '体质'],
        current: 0,		
    };
  },
  
  

  methods: {
    onClickItem(e) {
      if (this.current != e.currentIndex) {
        this.current = e.currentIndex;
      }
    },	  
  }
};
</script>

<style>
.container {
  padding: 20px; /* 容器内边距 */
  padding-bottom: 100px;
}

.input-container {
  display: flex;
  align-items: center; /* 垂直居中对齐 */
  margin-bottom: 10px; /* 根据需要调整间距 */
}
.input-label {
  margin-right: 8px; /* 根据需要调整间距 */
  font-size: 16px; /* 根据需要调整字体大小 */
}
input[type="text"] {
  /* 输入框的样式 */
  font-size: 16px; /* 根据需要调整字体大小 */
  padding: 8px; /* 根据需要调整内边距 */
  border: 1px solid #ccc; /* 根据需要调整边框样式 */
}

.question-container {
  margin-bottom: 20px; /* 问题间距 */
  background-color: #f9f9f9; /* 背景色 */
  border-radius: 8px; /* 边角圆滑 */
  padding: 15px; /* 问题内边距 */
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1); /* 盒阴影效果 */
}

.question-title {
  font-weight: bold; /* 标题加粗 */
  margin-bottom: 10px; /* 标题与选项间距 */
  color: #333; /* 标题颜色 */
}

.options-container {
  display: flex; /* 选项布局 */
  flex-direction: column; /* 选项竖直排列 */
}

.option {
  margin: 5px 0; /* 选项间距 */
  padding: 10px; /* 选项内边距 */
  border-radius: 5px; /* 选项边角圆滑 */
  transition: background-color 0.3s; /* 背景色过渡动画 */
}

.option:hover {
  background-color: #e6e6e6; /* 鼠标悬停时选项背景色 */
}

.save-button-container {
  display: flex; /* 按钮容器布局 */
  justify-content: center; /* 按钮居中对齐 */
  margin-top: 20px; /* 与问题间距 */
}

.save-button {
  padding: 10px 30px; /* 按钮内边距 */
  color: white; /* 按钮文字颜色 */
  background-color: #1a73e8; /* 按钮背景色 */
  border: none; /* 无边框 */
  border-radius: 4px; /* 按钮边角圆滑 */
  font-size: 16px; /* 文字大小 */
  cursor: pointer; /* 鼠标样式 */
}

.save-button:hover {
  background-color: #1669c7; /* 鼠标悬停时按钮背景色 */
}
</style>