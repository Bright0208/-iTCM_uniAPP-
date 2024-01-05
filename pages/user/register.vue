<template>
  <view class="container">
	<button class="avatar-container" open-type="chooseAvatar" @chooseavatar="onChooseAvatar">
	  <image class="avatar" :src="userInfo.avatarUrl"></image>
	</button>   
    <view class="info-item">
      <text class="info-label">昵称：</text>
	  <input class="info-value" type="nickname"  v-model="userInfo.nickname" @input="onInputNickname" placeholder="请输入昵称"/>
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
	  <input class="info-value" type="text" placeholder="请输入年龄" v-model="userInfo.name" />
	</view>
    <view class="info-item">
      <text class="info-label">手机：</text>
      <input class="info-value" type="number" placeholder="请输入手机号" v-model="userInfo.phone" @blur="validatePhone" />
      <text v-show="!phoneValid" class="error-msg">格式有误</text>
    </view>
    <view class="info-item">
      <text class="info-label">身份证：</text>
      <input class="info-value" type="text" placeholder="请输入身份证号" v-model="userInfo.idCard" @blur="validateIdCard" />
      <text v-show="!idCardValid" class="error-msg">格式有误</text>
    </view>
	<view class="info-notice">
	  除头像、昵称之外，其余信息提交后不可修改，请认真填写
	</view>
    <button class="submit-btn" @click="validateInfo">提交</button>
  </view>
</template>

<script>
const defaultAvatarUrl = 'https://mmbiz.qpic.cn/mmbiz/icTdbqWNOwNRna42FI242Lcia07jQodd2FJGIYQfG0LAJGFxM4FbnQP6yfMxBgJ0F3YRqJCJ1aPAK2dQagdusBZg/0'
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
        idCard: ''
      },
      phoneValid: true,
      idCardValid: true
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
        const token = uni.getStorageSync('token');
        if (token) {
          // 如果token存在，执行相关操作
          console.log('获取到的token:', token);
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
	// uni.uploadFile({
	//   filePath: avatarUrl,
	//   name: 'avatarImg',
	//   url: uploadUrl, // 服务器端接收图片的路径
	//   success: function(res) {
	//     console.log(res); // 发送成功回调
	//   },
	//   fail: function(res) {
	//     console.log(res); // 发送失败回调，可以在这里了解失败原因
	//   }
	// }); 
	
	 },	  
    // 输入框输入或选择微信昵称
    onInputNickname(event) {
      this.userInfo.nickname = event.target.value;
	  console.log("this.userInfo.nickname");
	  console.log(this.userInfo.nickname);
    },	  
    onGenderChange(event) {
      this.userInfo.genderIndex = event.target.value;
    },
    validatePhone() {
      this.phoneValid = /^1\d{10}$/.test(this.userInfo.phone);
    },
    validateIdCard() {
      this.idCardValid = /^[1-9]\d{5}[1-9]\d{3}((0[1-9])|(1[0-2]))(([0-2][1-9])|10|20|30|31)\d{3}([0-9]|X)$/.test(this.userInfo.idCard);
    },
    validateInfo() {
      // 可以在这里做提交前的最终验证，以确保所有的信息都是有效的
      if (this.phoneValid && this.idCardValid) {
        // 提交信息
        console.log('提交的用户信息：', this.userInfo);
        uni.showToast({
          title: '验证通过，信息已提交',
          icon: 'success',
          duration: 2000
        });
      } else {
        uni.showToast({
          title: '信息填写有误，请检查',
          icon: 'none',
          duration: 2000
        });
      }
    }
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
</style>