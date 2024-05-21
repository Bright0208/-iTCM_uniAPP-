<template>
	<view class="container">
		<view class="header">
			<p class="title">请阅读下面五个元音</p>
			<text class="vowels" name="" id="" cols="30" rows="10">A、E、I、O和U</text>
		</view>
		<view class="button-group">
			<button v-if="voicePath" @tap="playVoice">播放</button>
			<button v-if="voicePath" @tap="save">保存</button>
			<button v-if="voicePath" @tap="delete_voice">删除</button>
		</view>
		<view class="content">
			<all-speech class="all-speech" @startRecord="start" @endRecord="end" @cancelRecord="cancel"></all-speech>
		</view>
		<tabBar ref="customTabBar" selectedIndex="2"></tabBar>
	</view>
</template>

<script>
	import tabBar from "@/pages/tabBar/tabBar.vue"
	const recorderManager = uni.getRecorderManager();
	const innerAudioContext = uni.createInnerAudioContext();
	innerAudioContext.autoplay = true;
	export default {
		components:{
		 	tabBar //引用对应的组件
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
		onLoad() {
			let self = this;
			recorderManager.onStop(function (res) {
				console.log('recorder stop' + JSON.stringify(res));
				self.voicePath = res.tempFilePath;
			});
		},	
		data() {
			return {
				voicePath: ''				
			}
		},
		methods: {
			startRecord() {
			console.log('开始录音');

			recorderManager.start();
			},
			endRecord() {
				console.log('录音结束');
				recorderManager.stop();
			},
			playVoice() {
				console.log('播放录音');
				console.log(this.voicePath);
				
				if (this.voicePath) {
					innerAudioContext.src = this.voicePath;
					innerAudioContext.play();
				}
			},
			
			
	        start() {
	            // 开始录音
	        },
	        end(event) {
	            // 结束录音并处理得到的录音文件
	            // event中，app端仅有tempFilePath字段，微信小程序还有duration和fileSize两个字段
				this.voicePath = event.tempFilePath;
	        },
	        cancel() {
	            // 用户取消录音
	        },		
			delete_voice(){
				this.voicePath='';
			},
			save(){
				const token = uni.getStorageSync('token');
				const userInformation = uni.getStorageSync('userInformation');
				console.log(token)
				uni.uploadFile({
				  url: 'http://8.138.22.54:9001/wen2zhen/upload', // 后台接口
				  filePath: this.voicePath, // 临时路径
				  name: 'file', // 后台接收的文件参数名
				  formData: {
				         'token': token ,// 将token包含在formData中
						 'userInformation':userInformation
				       },
				  success(uploadRes) {
				    // 上传成功的回调
					uni.showToast({
					  title: '结果已保存',
					  icon: 'success',
					  duration: 1000
					}); 
				  },
				  fail(err) {
				    // 上传失败的回调
				    console.error('上传失败', err);
				  }
				});
			},
		}
	}
</script>

<style>
.container {
  position: relative;
  display: flex;
  flex-direction: column;
}

.header {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center; /* 垂直居中 */
  padding: 20px;
}

.title {
  font-size: 24px;
  margin-bottom: 10px;
}

.vowels {
  font-size: 20px;
  font-weight: bold;
}

.content {
  display: flex;
  justify-content: center;
}

.all-speech {
  padding-top: 60vh;
}

.button-group {
  display: flex;
  justify-content: center;
  align-items: center;
}
</style>
