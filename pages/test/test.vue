<template>
  <view class="container">
    <view class="logo-container">
      <image class="logo" src="https://img2.baidu.com/it/u=2663264561,635415515&fm=253&fmt=auto&app=138&f=JPEG?w=657&h=500" mode="aspectFit"></image>
    </view>
	<view class="button-container">
      <button class="button-common login-btn" @click="onWechatLogin">微信登录</button>
      <!-- <navigator url="/pages/user/register" class="button-common register-btn">注册账号</navigator> -->
	</view>
  </view>
</template>

<script>
export default {
  methods: {
    onWechatLogin() {
      // 显示确认弹窗
      this.showConfirmDialog();
    },
    showConfirmDialog() {
      // 这里使用了uni-app的API作为示例
      uni.showModal({
        title: '微信登录',
        content: '是否授权微信登录',
        success: (res) => {
          if (res.confirm) {
            console.log('用户点击确定');
            // 在这里执行微信登录的相关代码
            this.wechatLogin();
          } else if (res.cancel) {
            console.log('用户点击取消');
            // 处理取消操作
          }
        }
      });
    },	
  wechatLogin() {
    uni.login({
      provider: 'weixin',
      success: (res) => {
		  console.log(res.code);
        if (res.code) {
          uni.request({
            url: 'http://121.41.80.48:9001/wxlogin/login',
            method: 'POST',
            data: {
              code: res.code
            },
            success: (response) => {
			  const notRegister = response.data.notRegister;
			  const token = response.data.token;
			  const identity = response.data.identity;
			  console.log(identity);
			  uni.setStorageSync('token', token); //保存token
			  if (notRegister) {
			          // 如果notRegister等于1，显示提示信息，并在3秒后跳转到注册页面
			          uni.showToast({
			              title: '    首次登录，请先完善信息    ',
			              icon: 'none', // 不显示图标
			              duration: 2000, // 持续时间2秒
			              success: () => {
			                  setTimeout(() => {
			                      // 在提示信息显示结束后跳转
			                      uni.redirectTo({
			                          url: '/pages/user/register'
			                      });
			                  }, 2000); // 2秒后执行页面跳转，确保提示信息已经展示完毕
			              }
			          });
			      } else if (identity==2) {
						uni.setStorageSync('identify','user')
						uni.switchTab({
						    url: '/pages/home/wangzhen'
						});
			          // 用户已注册，执行其他逻辑
			      }else{
					 uni.setStorageSync('identify','doctor')
					 uni.switchTab({
					     url: '/pages/doctor/wangzhen/wangzhen'
					 }); 
					  console.log(token);
				  }
				  
              
            },
            fail: (err) => {
              console.log('登录失败：' + err.errMsg);
            }
          });
        } else {
          console.log('登录失败：' + res.errMsg);
        }
      },
      fail: (err) => {
        console.log('登录失败：' + err.errMsg);
      }
    });
  },	 
	
  }
};
</script>

<style>
.page {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  height: 100%;
  background: linear-gradient(135deg, #fdfcfb 0%, #e2d1c3 100%);
}

.logo-container {
  margin-top: 80px;
  margin-bottom: 80px;
  display: flex; /* 添加flex布局 */
  justify-content: center; /* 水平居中 */
  align-items: center; /* 垂直居中 */
  width: 100%; /* 确保容器宽度为100% */
}

.logo {
  width: 120px;
  height: 120px;
  /* 根据logo形状可能需要调整为 'width' 或 'height' */
  border-radius: 50%; /* 圆形logo的边框半径 */
}

.button-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  width: 100%;
}

/* 共同的样式 */
.button-common {
  display: flex;
  justify-content: center;
  align-items: center;
  width: 80%;
  height: 50px; /* 设置一个固定高度 */
  margin-top: 20px; /* 确保按钮与按钮之间的间距相同 */
  margin-bottom: 20px;
  color: #ffffff;
  font-size: 18px;
  font-weight: bold;
  border-radius: 25px;
  text-align: center; /* 确保文本居中 */
  box-shadow: 0 5px 15px rgba(0, 0, 0, 0.2);
  transition: transform 0.2s, box-shadow 0.2s;
}

/* 应用于<button>的特定样式 */
.login-btn {
  background: #07C160; /* 微信绿 */
  border: none;
  outline: none;
}

/* 应用于<navigator>的特定样式 */
.register-btn {
  background: #007AFF; /* iOS 蓝 */
  border: 1px solid #007AFF; /* 有边框 */
  box-sizing: border-box; /* 确保边框宽度计算在内 */
  text-decoration: none; /* 移除超链接的下划线 */
}

/* 调整点击态 */
.button-common:active {
  transform: translateY(2px);
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.2);
}
</style>