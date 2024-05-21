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
      <text class="info-value" >{{userInfo.name}}</text>
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
      <text class="info-value" >{{userInfo.phone}}</text>
    </view>
    <view class="info-item">
      <text class="info-label">身份证：</text>
      <text class="info-value" >{{userInfo.idCard}}</text>
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
        genderIndex: 0,//初始化默认选中第一项，即‘男’
		age:'',
		name:'',
		phone:'',
		idCard:'',
      },
      phoneValid: true,
      idCardValid: true
    };
  },
  
  onLoad() {
	  //页面加载时获取用户信息
	this.getUserInfo()
  },
  
  methods: {
	//获取用户信息
	getUserInfo(){
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
			this.userInfo.avatarUrl = response.data.user.avatarUrl;
			this.userInfo.nickname = response.data.user.nickName;
			this.userInfo.genderIndex = response.data.user.gender;
			this.userInfo.age = response.data.user.age;
			this.userInfo.name = response.data.user.name;
			this.userInfo.phone = response.data.user.mobile;
			this.userInfo.idCard = response.data.user.idcard;
		})
		.catch((error) => {
		    // 请求失败的处理
		    console.error(error);
		});	
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
	
	handleBlur(event){
	  this.userInfo.nickname=event.target.value;
	},
	
    onGenderChange(event) {
      this.userInfo.genderIndex = event.target.value;
    },

    validateInfo() {
	if(this.userInfo.phone!="" && this.userInfo.idCard!="" ){
      // 可以在这里做提交前的最终验证，以确保所有的信息都是有效的
      if (this.phoneValid && this.idCardValid) {
        // 提交信息
		
        console.log('提交的用户信息：', this.userInfo);
        uni.showToast({
          title: '验证通过，信息已提交',
          icon: 'success',
          duration: 2000
        });
		uni.request({
		  url: 'http://8.138.22.54:9001/user/register',
		  method: 'POST',
		  data: {
			token: uni.getStorageSync('token'),
			avatarUrl: this.userInfo.avatarUrl,
			nickname: this.userInfo.nickname,
			name: this.userInfo.name,
			genderIndex: this.userInfo.genderIndex,//0男1女
			age: this.userInfo.age,
			phone: this.userInfo.phone,
			idCard:this.userInfo.idCard
		  },
		  success: (res) => {
			  if(res.statusCode >= 200 && res.statusCode < 300){
			  uni.showToast({
			    title: '修改成功',
			    icon: 'none',
			    duration: 1000
			  });
			  setTimeout(() => {
			       uni.switchTab({
			           url: '/pages/user/PersonalInfo'
			       });
			      }, 500);

			  
			}else{
				uni.showToast({
				  title: '修改失败',
				  icon: 'none',
				  duration: 1000
				});
			}		   
		  },
		  fail: (err) => {
		    console.log('修改失败：' + err.errMsg);
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