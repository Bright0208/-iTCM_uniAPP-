<template>
  <view class="container">
    <view class="userinfo">
		<button class="avatar-wrapper" open-type="chooseAvatar" @chooseavatar="onChooseAvatar">
		  <image class="avatar" :src="avatarUrl"></image>
		</button> 
		<input type="nickname" class="weui-input" v-model="nickname" @input="onInputNickname" placeholder="请输入昵称"/>
    </view>
    <button @click="wechatLogin">登录</button>
	
  </view>
</template>

<script>
const defaultAvatarUrl = 'https://mmbiz.qpic.cn/mmbiz/icTdbqWNOwNRna42FI242Lcia07jQodd2FJGIYQfG0LAJGFxM4FbnQP6yfMxBgJ0F3YRqJCJ1aPAK2dQagdusBZg/0'
export default {
  data() {
    return {
	  avatarUrl: defaultAvatarUrl,
	  nickname: "微信用户",
	  hasUserInfo: false,
	  isLogged: false,
    };
  },
  methods: {
	// 选择头像
	      onChooseAvatar(e) {
	        const {
	          avatarUrl
	        } = e.detail;
	        this.avatarUrl = avatarUrl;
			
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
	        this.nickname = event.target.value;
	      },
		  wechatLogin() {
		    uni.login({
		      provider: 'weixin',
		      success: (res) => {
		    	     		  console.log(res.code);
		        if (res.code) {
		          uni.request({
		            url: 'http://localhost:9001/wxlogin/login',
		            method: 'POST',
		            data: {
		              code: res.code
		            },
		            success: (response) => {
		             const token = response.data.token;
		    	     			  console.log(response);
		  	     			  this.nickname = response.data.nickName;
		  	     			  this.avatarUrl = response.data.avatarUrl;
		             uni.setStorageSync('token', token);
		  	     			  
		              // 其他逻辑处理
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
		  
		 getUserInfo212() {
		     // var _this = this
		     wx.showModal({
		       title: '温馨提示',
		       content: '亲，授权微信登录后才能正常使用小程序功能',
		       success(res) {
		         //如果用户点击了确定按钮
		         if (res.confirm) {
					this.wechatLogin();
		         } else if (res.cancel) {
		           //如果用户点击了取消按钮
		           wx.showToast({
		             title: '您拒绝了请求',
		             icon: 'error',
		             duration: 2000
		           });
		           return;
		         }
		       }
		     });
		 }, 
		 
  }, 
};

	






</script>

<style>
.container {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 50rpx;
}

.userinfo {
  margin-bottom: 50rpx;
}

.avatar {
  width: 100rpx;
  height: 100rpx;
  border-radius: 50%;
  margin-bottom: 20rpx;
}

.nickname {
  font-size: 28rpx; 
  color: #333;
}

button {
  border: none;
  padding: 10rpx 20rpx;
  background-color: #1aad19;
  color: white;
  border-radius: 5rpx;
}
</style>