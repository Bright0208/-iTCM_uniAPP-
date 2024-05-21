<template>
  <view class="container">
    <view class="input-group">
	  <text>手机：</text>
	  <uni-easyinput class="input" trim="all" @blur="checkPhone" v-model="phone" placeholder="手机号" ></uni-easyinput>
      <text v-if="phoneError" class="error-msg" >{{ phoneError }}</text>
    </view>
<!--    <view class="input-group">
      <text>邮箱：</text>
      <input type="email" placeholder="邮箱" @blur="checkEmail" v-model="email"/>
      <text v-if="emailError" class="error-msg">{{ emailError }}</text>
    </view> -->
    <view class="input-group">
	  <text>密码：</text>
	  <uni-easyinput class="input" type="password" @blur="checkPassword" v-model="password" placeholder="密码"></uni-easyinput>
      <text v-if="passwordError" class="error-msg">{{ passwordError }}</text>
    </view>
    <view class="input-group">
	  <text>密码：</text>
	  <uni-easyinput class="input" type="password" @blur="checkConfirmPassword" v-model="confirmPassword" placeholder="确认密码"></uni-easyinput>
      <text v-if="confirmPasswordError" class="error-msg">{{ confirmPasswordError }}</text>
    </view>
    <view class="input-group">
	  <text>验证码：</text>
	  <uni-easyinput class="input"  @blur="checkCaptcha" v-model="captcha" placeholder="验证码"></uni-easyinput>
      <button class="info-button" @click="startCountDown" :disabled="disableButton">{{ disableButton ? countDown + 's ' : '获取验证码' }}</button>
    </view>
    <button @click="register">注册</button>
  </view>
</template>

<script>
export default {
  data() {
    return {
      phone: '',
      email: '',
      password: '',
      confirmPassword: '',
      captcha: '',
      phoneError: '',
      emailError: '',
      passwordError: '',
      confirmPasswordError: '',
      captchaError: '',
	  countDown: 60, // 倒计时初始值，单位为秒
	  disableButton: false, // 控制按钮是否可点击
    };
  },
  methods: {
    // 获取验证码
    getCaptcha() {
      // 模拟发送验证码操作
      console.log('验证码已发送');
    },
    // 注册操作，这里应包含必填项验证及提交前的最终验证
    register() {
      if (this.validateAll()) {
		  uni.request({
          url: 'http://8.138.22.54:9001/user/account_register',
          method: 'POST',
          data: {
        	phone: this.phone,
        	password:this.password,
        	captcha:this.captcha,
          },
          success: (res) => {
			  const token = res.data.token;
			  const identity = res.data.identity;
			  uni.setStorageSync('token', token); //保存token
        	  if(res.statusCode >= 200 && res.statusCode < 300){
        	      uni.showToast({
        	        title: '注册成功，即将跳转',
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
        		  }
        	}else{
        		uni.showToast({
        		  title: '注册失败',
        		  icon: 'none',
        		  duration: 1000
        		});
        	}		   
          },
          fail: (err) => {
            console.log('注册失败：' + err.errMsg);
          }
        });
      }
    },
    // 手机号验证
    checkPhone() {
      const regex = /^1[3-9]\d{9}$/;
      this.phoneError = regex.test(this.phone) ? '' : '请输入有效的手机号';
    },
    // 邮箱验证
    checkEmail() {
      const regex = /^[a-zA-Z0-9._-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,6}$/;
      this.emailError = regex.test(this.email) ? '' : '请输入有效的邮箱地址';
    },
    // 密码验证
    checkPassword() {
      this.passwordError = this.password ? '' : '密码不能为空';
    },
    // 确认密码验证
    checkConfirmPassword() {
      this.confirmPasswordError = this.confirmPassword === this.password ? '' : '两次输入的密码不一致';
    },
    // 验证码验证
    checkCaptcha() {
      this.captchaError = this.captcha ? '' : '验证码不能为空';
    },
    // 验证所有输入是否正确
    validateAll() {
      this.checkPhone();
      // this.checkEmail();
      this.checkPassword();
      this.checkConfirmPassword();
      this.checkCaptcha();

      return !(this.phoneError  || this.passwordError || this.confirmPasswordError || this.captchaError);
    },
	
	//按钮倒计时
	startCountDown() {
	    this.disableButton = true; // 设置按钮不可点击
	
	    let timer = setInterval(() => {
	      if (this.countDown > 0) {
	        this.countDown--;
	      } else {
	        clearInterval(timer);
	        this.countDown = 60; // 重置倒计时
	        this.disableButton = false; // 恢复按钮可点击
	      }
	    }, 1000);
		
		console.log(this.phone)
		
		uni.request({
		    url: 'http://8.138.22.54:9001/aliyun/msm/send/'+this.phone,
		    method: 'GET',
		})
		.then((response) => {
			console.log(response);
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
/* 样式按需定义 */
.container {
  display: flex;
  flex-direction: column;
  padding: 20px;
  margin-top: 50rpx;
}

.input-group {
  display: flex;
  flex-direction: row;
  /* align-items: center; */
  margin-bottom: 10px;
  flex: 1;
  margin-top: 10px;
}
.input{
	display: block;
}
text {
  margin-right: 10px;
  /* width: 30px; */
  align-items: center;  
}

.input-row input {
  flex: 1;
  border: 0; /* 去除输入框边框 */
  outline: none; /* 去除点击输入框时的外边框 */
  margin-right: 10px;
}

.error-msg {
  color: red;
  font-size: 12px;
  padding-left: 10px;
}
.info-button{
	font-size: 16px;
	background-color: #4fbcff;
	border: 1px solid #ddd;
	border-radius: 1px;
	padding: 0px;
	margin: 0px;
}
button {
  flex: 1;
  padding: 10px;
  margin: 5px 0;
  border: 1px solid #ddd;
  border-radius: 4px;
  background-color: #007aff;
  color: white;
}
</style>