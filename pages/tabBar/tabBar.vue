<template name="tabBar">
    <view class="tab-bar">
        <view v-for="(item,index) in list" :key="index" class="tab-bar-item" @click="switchTab(item, index)">
            <image class="tab_img" :src="currentIndex == index ? item.selectedIconPath : item.iconPath"></image>
            <view class="tab_text" :style="{color: currentIndex == index ? selectedColor : color}">{{item.text}}</view>
        </view>
    </view>
</template>

<script>
    export default {
		name:"tabBar",
        props: {
            selectedIndex: { // 当前选中的tab index
                default: 0
            },
        },
        data() {
            return {
                color: "#666666",
                selectedColor: "#00BAB2",
                list: [],
                currentIndex:0,
            }
        },
        created() {
            this.currentIndex = this.selectedIndex;
            
            var _this = this
            
            if (uni.getStorageSync('identify') == 'user') {
                //用户
                _this.list = [{
                        "pagePath": "/pages/home/wangzhen",
                        "iconPath": "/static/tab/wangzhen_black.png",
                        "selectedIconPath": "/static/tab/wangzhen_blue.png",
                        "text": "望诊"
                    },
                    {
                        "pagePath": "/pages/home/wenzhen",
                        "iconPath": "/static/tab/wenzhen_black.png",
                        "selectedIconPath": "/static/tab/wenzhen_blue.png",
                        "text": "问诊"
                    },
					
					{
					    "pagePath": "/pages/user/PersonalInfo",
					    "iconPath": "/static/tab/wode_black.png",
					    "selectedIconPath": "/static/tab/wode_blue.png",
					    "text": "我的"
					}
                ]
            } else {
                //医管
                _this.list = [{
                        "pagePath": "/pages/doctor/wangzhen/wangzhen",
                        "iconPath": "/static/tab/wangzhen_black.png",
                        "selectedIconPath": "/static/tab/wangzhen_blue.png",
                        "text": "标注"
                    },
                    // {
                    //     "pagePath": "/pages/nurse/nurselist",
                    //     "iconPath": "/static/tab/nurse_n.png",
                    //     "selectedIconPath": "/static/tab/nurse_s.png",
                    //     "text": "护士"
                    // },
                    {
                        "pagePath": "/pages/doctor/PersonalInfo",
                        "iconPath": "/static/tab/wode_black.png",
                        "selectedIconPath": "/static/tab/wode_blue.png",
                        "text": "我的"
                    }
                ]
            }
        },
		
		
		
		
        methods: {
            switchTab(item, index) {
                this.currentIndex = index;
                
                let url = item.pagePath;
                uni.switchTab({url:url})
            },
            updateActiveTabIndex() {
              // 获取当前页面栈
              const pages = getCurrentPages();
              // 获取当前页面的实例
              const currentPage = pages[pages.length - 1];
              // 获取当前页面的路由
              const url = `/${currentPage.route}`;
           
              // 根据当前页面的路由更新选中状态
              const tabIndex = this.list.findIndex(item => url === item.pagePath);
              if (tabIndex !== -1) {
                this.currentIndex = tabIndex;
              }
            },
        }
    }
</script>

<style lang="scss">
    .tab-bar {
        position: fixed;
        bottom: 0;
        left: 0;
        right: 0;
        height: 100rpx;
        background: white;
        display: flex;
        justify-content: center;
        align-items: center;
        padding-bottom: env(safe-area-inset-bottom); // 适配iphoneX的底部

        .tab-bar-item {
            flex: 1;
            text-align: center;
            display: flex;
            justify-content: center;
            align-items: center;
            flex-direction: column;
            .tab_img {
                width: 37rpx;
                height: 41rpx;
            }
            .tab_text {
                font-size: 20rpx;
                margin-top: 9rpx;
            }
        }
    }
</style>