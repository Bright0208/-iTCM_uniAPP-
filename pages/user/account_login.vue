<template>
  <view class="container">
    <view class="form-group">
      <text class="label">手机：</text>
	  <uni-easyinput class="input" trim="all" v-model="phone" placeholder="请输入手机号" ></uni-easyinput>
    </view>
    <view class="form-group">
      <text class="label">密码：</text>
	  <uni-easyinput class="input" type="password" v-model="password" placeholder="请输入密码"></uni-easyinput>
	  <!-- <text class="label">验证码：</text> -->
    </view>
<!--    <view class="form-group captcha-group">
	  <view class="captcha-container">
		<input class="input captcha-input" type="text" v-model="captcha" placeholder="请输入验证码" />
		<image class="captcha-btn" @click="flashCaptcha" src="/static/logo.png"/>
	  </view>
    </view> -->
    <button class="login-btn" @click="login">登录</button>
  </view>
</template>

<script>
export default {
  data() {
    return {
      phone: '',
      password: '',
      captcha: '',
      isSendingCaptcha: false,
      captchaText: '发送验证码',
    };
  },
  methods: {
    sendCaptcha() {
      // 发送验证码逻辑
      this.isSendingCaptcha = true;
      // 模拟发送验证码，延迟2秒后恢复发送按钮
      setTimeout(() => {
        this.isSendingCaptcha = false;
      }, 2000);
    },
    login() {
      // 登录逻辑
      uni.request({
        url: 'http://8.138.22.54:9001/user/login',
        method: 'POST',
        data: {
			phone: this.phone,
			password: this.password,
        },
        success: (res) => {
		  const token = res.data.token;
		  const identity = res.data.identity;
		  uni.setStorageSync('token', token); //保存token
      	  if(res.statusCode >= 200 && res.statusCode < 300){
      	      uni.showToast({
      	        title: '登录成功，即将跳转',
      	        icon: 'none',
      	        duration: 1000
      	      });
			  
			  if (identity==2) {
			  		uni.setStorageSync('identify','user')
			  		uni.navigateTo({
			  		    url: '/pages/user/personalInformation'
			  		});
			      // 用户已注册，执行其他逻辑
			  }else{
			  		uni.setStorageSync('identify','doctor')
			  		uni.switchTab({
			  		    url: '/pages/doctor/wangzhen/wangzhen'
			  		}); 
			  		 console.log(token);
			  }
      	 }else{
      		uni.showToast({
      		  title: '登录失败',
      		  icon: 'none',
      		  duration: 1000
      		});
      	}		   
        },
        fail: (err) => {
          console.log('登录失败：' + err.errMsg);
        }
      });
    },
  },
};
</script>

<style>
.container {
  max-width: 400px;
  margin: 50px auto;
  padding: 20px;
  border-radius: 8px;
  box-shadow: 0 4px 10px rgba(0,0,0,0.15);
  background-color: #ffffff;
}

.form-group {
  margin-bottom: 15px;
}
.captcha-group {
   display: flex;       /* 使用flex布局 */
   align-items: center; /* 子项垂直居中 */
}
.captcha-container {
  display: flex;       /* 使用flex布局 */
  align-items: center; /* 子项垂直居中 */
}
.captcha-input {
  margin-right: 10px; /* 输入框和图片之间的间隔 */
}
.label {
  display: block;
  margin-bottom: 5px;
  font-size: 16px;
  color: #333333;
}

.input {

  font-size: 18px;
}

.input:focus {
  outline: none;
  border-color: #007BFF;
  box-shadow: 0 0 0 2px rgba(0,123,255,.25);
}

.captcha-btn {
  width: 400rpx;
  display: block;
  cursor: pointer;
  height: 80.4rpx;
}

.login-btn {
  width: 100%;
  padding: 10px;
  border: none;
  border-radius: 4px;
  background-color: #007BFF;
  color: white;
  font-size: 16px;
  cursor: pointer;
  outline: none;
}

.login-btn:hover {
  background-color: #0056b3;
}

.login-btn:active {
  background-color: #00408b;
}

.login-btn:focus {
  box-shadow: 0 0 0 2px rgba(0,123,255,.5);
}
</style>