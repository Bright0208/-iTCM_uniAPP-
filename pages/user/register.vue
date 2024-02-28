<template>
  <view class="container">
	<button class="avatar-container" open-type="chooseAvatar" @chooseavatar="onChooseAvatar">
	  <image class="avatar" :src="userInfo.avatarUrl"></image>
	</button>   
    <view class="info-item">
      <text class="info-label">昵称：</text>
	  <input class="info-value" type="nickname"  v-model="userInfo.nickname" @blur="handleBlur" @input="onInputNickname" placeholder="请输入昵称"/>
    </view>
    <view class="info-item">
      <text class="info-label">姓名：</text>
      <input class="info-value" type="text" placeholder="请输入姓名" v-model="userInfo.name" />
    </view>
    <view class="info-item">
      <text class="info-label">性别：</text>
      <picker class="info-picker" mode="selector" :range="genderArray" v-model="userInfo.genderIndex" @change="onGenderChange">
        <view class="picker">
          {{ genderArray[userInfo.genderIndex] }}
        </view>
      </picker>
    </view>
	<view class="info-item">
	  <text class="info-label">年龄：</text>
	  <input class="info-value" type="text" placeholder="请输入年龄" v-model="userInfo.age" />
	</view>
    <view class="info-item">
      <text class="info-label">手机：</text>
      <input class="info-value" type="number" placeholder="请输入手机号" v-model="userInfo.phone" @blur="validatePhone" />
      <text v-show="!phoneValid" class="error-msg">格式有误</text>
    </view>
<!-- 	<view class="info-item">
	  <text class="info-label">邮箱：</text>
	  <input class="info-value" type="number" placeholder="请输入邮箱号" v-model="userInfo.email" @blur="validateEmail" />
	  <text v-show="!emailValid" class="error-msg">格式有误</text>
	</view> -->
<!--    <view class="info-item">
      <text class="info-label">身份证：</text>
      <input class="info-value" type="text" placeholder="请输入身份证号" v-model="userInfo.idCard" @blur="validateIdCard" />
      <text v-show="!idCardValid" class="error-msg">格式有误</text>
    </view> -->
	<view class="info-item">
	  <text class="info-label">密码：</text>
	  <input class="info-value" type="password" placeholder="密码" @blur="checkPassword" v-model="userInfo.password"/>
	  <text v-if="passwordError" class="error-msg">{{ passwordError }}</text>
	</view>
	<view class="info-item">
	  <text class="info-label">确认密码：</text>
      <input class="info-value" type="password" placeholder="确认密码" @blur="checkConfirmPassword" v-model="confirmPassword"/>
      <text v-if="confirmPasswordError" class="error-msg">{{ confirmPasswordError }}</text>
	</view>
	<view class="info-item">
	  <text class="info-label">验证码：</text>
	  <input class="info-value" type="text" placeholder="验证码"  v-model="captcha"/>
	  <button class="info-button" @click="startCountDown" :disabled="disableButton">{{ disableButton ? countDown + 's 后重新获取' : '获取验证码' }}</button>
	</view>
	<view class="info-notice">
	  除头像、昵称、性别、年龄之外，其余信息提交后不可修改，请认真填写
	</view>
    <!-- 用户服务协议和隐私政策链接 -->
    <view class="agreements">
      <view class="checkbox-container" >
        <checkbox v-model="isAgreementChecked" class="agreement-checkbox" @click="toggleAgreementCheck" ></checkbox>
        <text class="checkbox-label" >我已阅读并同意</text>
      </view>
      <text class="agreement-link" @click="openAgreement('user')">《用户服务协议》</text>
      <text>和</text>
      <text class="agreement-link" @click="openAgreement('privacy')">《隐私政策》</text>
    </view>	
    <button class="submit-btn"  @click="validateInfo"  >提交</button>
  </view>
</template>

<script>
const defaultAvatarUrl = 'https://mmbiz.qpic.cn/mmbiz/icTdbqWNOwNRna42FI242Lcia07jQodd2FJGIYQfG0LAJGFxM4FbnQP6yfMxBgJ0F3YRqJCJ1aPAK2dQagdusBZg/0'
const token=""
export default {
  data() {
    return {
      genderArray: ['男', '女'],
      userInfo: {
		avatarUrl:defaultAvatarUrl,
        nickname: '',
        name: '',
        genderIndex: 0,//初始化默认选中第一项，即‘男’
		age:'',
        phone: '',
		// email:'',
        // idCard: ''
		password:'',
      },
      phoneValid: true,
	  emailValid: true,
      idCardValid: true,
	  confirmPassword: '',
	  passwordError: '',
	  confirmPasswordError: '',
	  captcha: '',//验证码
	  countDown: 60, // 倒计时初始值，单位为秒
      disableButton: false, // 控制按钮是否可点击
	  isAgreementChecked: false, // 初始状态未勾选
    };
  },
  
  onLoad() {
    // 在页面加载时获取存储的token
    this.getToken();
  },
  
  methods: {
	 //获取到JWTtoken值 
    getToken() {
      // 使用try-catch来处理可能的异常
      try {
        this.token = uni.getStorageSync('token');
        if (this.token) {
          // 如果token存在，执行相关操作
          console.log('获取到的token:', this.token);
        } else {
          // token不存在的处理逻辑
          console.warn('未找到token，可能需要重新登录');
        }
      } catch (e) {
        // 获取token过程中出现错误的处理逻辑
        console.error('获取token失败:', e);
      }
    },	  
	   
	  
	// 选择头像
	 onChooseAvatar(e) {
	   const {
	     avatarUrl
	   } = e.detail;
	   this.userInfo.avatarUrl = avatarUrl;

	 },	  
    // 输入框输入或选择微信昵称
    onInputNickname(event) {
      this.userInfo.nickname = event.target.value;
    },
	//通过blur保存昵称
	handleBlur(event){
	  this.userInfo.nickname=event.target.value;
	},
	//修改性别
    onGenderChange(event) {
      this.userInfo.genderIndex = event.target.value;
    },
	//验证手机格式
    validatePhone() {
      this.phoneValid = /^1\d{10}$/.test(this.userInfo.phone);
    },
	//验证邮箱格式
	validateEmail() {
	  this.emailValid = /^[\w-]+(\.[\w-]+)*@[\w-]+(\.[\w-]+)+$/.test(this.userInfo.email);
	},
	//验证身份证格式
    validateIdCard() {
      this.idCardValid = /^[1-9]\d{5}[1-9]\d{3}((0[1-9])|(1[0-2]))(([0-2][1-9])|10|20|30|31)\d{3}([0-9]|X)$/.test(this.userInfo.idCard);
    },
	// 密码验证
	checkPassword() {
	  this.passwordError = this.userInfo.password ? '' : '密码不能为空';
	},
	// 确认密码验证
	checkConfirmPassword() {
	  this.confirmPasswordError = this.confirmPassword === this.userInfo.password ? '' : '两次输入的密码不一致';
	},
	// 获取验证码
	getCaptcha() {
	  // 模拟发送验证码操作
	  console.log('验证码已发送');
	},
	
	//验证信息并提交到后台
    validateInfo() {
	if(this.userInfo.phone!="" && this.userInfo.name!="" ){
      // 可以在这里做提交前的最终验证，以确保所有的信息都是有效的
	  if(this.isAgreementChecked==false){
		uni.showToast({
		  title: '请勾选协议',
		  icon: 'success',
		  duration: 2000
		});  
	  }else if (this.phoneValid) {
        // 提交信息
        console.log('提交的用户信息：', this.userInfo);
        uni.showToast({
          title: '验证通过，信息已提交',
          icon: 'none',
          duration: 3000
        });
		uni.request({
		  url: 'http://localhost:9001/user/register',
		  method: 'POST',
		  data: {
			token: this.token, //里面有 openid
			avatarUrl: this.userInfo.avatarUrl,
			nickname: this.userInfo.nickname,
			name: this.userInfo.name,
			genderIndex: this.userInfo.genderIndex,//0男1女
			age: this.userInfo.age,
			phone: this.userInfo.phone,
			// email: this.userInfo.email,
			// idCard:this.userInfo.idCard
			password:this.userInfo.password,
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
				 		uni.switchTab({
				 		    url: '/pages/home/wangzhen'
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
		
      } else {
        uni.showToast({
          title: '信息填写有误，请检查',
          icon: 'none',
          duration: 2000
        });
      }
	 } else{
		uni.showToast({
		  title: '请提交完整信息',
		  icon: 'none',
		  duration: 2000
		}); 
	 } 
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
		
		uni.request({
		    url: 'http://localhost:9005/aliyun/msm/send/'+this.userInfo.phone,
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
	
	//检查是否勾选隐私协议
	toggleAgreementCheck() {
	  this.isAgreementChecked = !this.isAgreementChecked;
	},	
	
	//打开隐私协议的方法
    openAgreement(type) {
      if (type === 'user') {
        // 打开用户服务协议页面或视图
		uni.navigateTo({
			url:"/pages/user/agreement/user"
		})
      } else if (type === 'privacy') {
        // 打开隐私政策页面或视图
		uni.navigateTo({
			url:"/pages/user/agreement/privacy"
		})
      }
    },	
  }
};
</script>

<style>
.container {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 20px;
}
.avatar-container {
  border: none;
  outline: none;
  background-color: transparent;	  
  margin-bottom: 30px;
  border-radius: 50%;
}
.avatar {
  width: 100px;
  height: 100px;
  border-radius: 50%;
}
.info-item {
  display: flex;
  align-items: center;
  margin-bottom: 20px;
  width: 100%;
}
.info-label {
  width: 60px;
  color: #333;
  
}
.info-value, .info-picker {
  flex: 1;
  /* border: 1px solid #ddd; */
  padding: 10px;
  border-radius: 5px;
  background-color: #fff;
}
.info-button{
	flex: 1;
	font-size: 14px;
}
.picker-display {
  padding-left: 5px; /* 与输入框文字对齐 */
}
.error-msg {
  color: red;
  padding-left: 10px;
}
.submit-btn {
  width: 100%;
  padding: 10px 0;
  font-size: 16px;
  background-color: #000;
  color: #fff;
  border-radius: 5px;
  border: none;
  margin-top: 20px; /* 与其他元素分开 */
}
.info-notice {
  font-size: 12px; /* 设置小字号大小 */
  color: #999; /* 设置字体颜色 */
  text-align: center; /* 文字居中 */
  padding: 10px 0; /* 上下间距，左右不设置 */
  margin-top: 20px; /* 如有需要，可设置间距 */
}
.agreements {
  text-align: center;
  margin-top: 20px; /* 或更多，根据你页面的需求 */
  padding-bottom: 20px; /* 在这个元素的底部增加内边距以避免被tabbar遮挡 */
}

.agreement-link {
  color: #1E90FF; /* 或你应用的主题色 */
  margin: 0 5px;
  /* 如果希望实现点击时有视觉反馈，可以添加如下样式变量 */
  /* 注意这可能需要根据实际的框架或平台进行调整 */
  --active-color: #555;
  --active-opacity: 0.7;
}
.checkbox-container {
  display: flex;
  align-items: center;
}

.checkbox-label {
  margin-left: 8px;
}



</style>