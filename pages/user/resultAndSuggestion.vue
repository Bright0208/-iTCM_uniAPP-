<template>
  <view class="container">
    <!-- 圆形图标 -->
    <view class="icon-wrapper">
      <image class="icon" src="https://itcm-wyu.oss-cn-guangzhou.aliyuncs.com/example/logo/wyu.png" mode="aspectFill"></image>
    </view>
    <!-- 诊断报告标题 -->
    <text class="information" >病历号:{{userInformation.user.medicalRecordNumber}}  身高:{{userInformation.user.height}}  体重:{{userInformation.user.weight}}  年龄:{{userInformation.user.age}}  性别:{{userInformation.user.gender}}  既往病史:{{userInformation.pastMedicalHistory}}</text>
    <!-- 内容 -->
    <text class="content">{{content|| "正在加载中..."}}</text>
  </view>
</template>
<script>
	export default {
		created() {
			this.ToAi()
		},
		data() {
			return {
				requestBody: {
				  model: 'Qwen1___5-7B-Chat-GPTQ-Int4',
				  messages: [
				    {
				      role: 'system',
				      content: '你的身份是一位经验丰富的老中医，精通望、闻、问、切四诊，能够根据患者的具体症状，准确分析患者可能的病因，并能提供相关的医学解释和可靠的医学资料支持。',
				    },
				    {
				      role: 'user',
				      content: '从中医的角度来看，面黄、舌苔白、脉弦滑等症状可能是什么原因？\n补充材料：年龄：23，性别：女，既往病史：无，家族病史：无，过敏史：无，吸烟史：无，饮酒史：无，体检：正常，家族史：无，职业：医生。',
				    },
				  ],
				},
				content	:'',
				userInformation:[],
			}
		},
		methods: {
			ToAi() {
				
				// this.content = uni.getStorageSync('content');
				
				
				const wenzhenPhysique = uni.getStorageSync('wenzhen');
				const tonguePhysique = uni.getStorageSync('tongue').join('，');
				const face = uni.getStorageSync('face');
				const chinColor = face.下巴
				const headColor = face.额头
				const rightCheekColor = face.右脸颊
				const leftCheekColor = face.左脸颊
				const noseColor = face.鼻子
				this.userInformation = JSON.parse(uni.getStorageSync('userInformation'));
				const pastMedicalHistory = this.userInformation.pastMedicalHistory
				const user = this.userInformation.user
				const content = `从中医的角度来看下巴：${chinColor}，右脸颊：${rightCheekColor}，左脸颊：${leftCheekColor}，额头：${headColor}，鼻子：${noseColor}，问诊结果是：${wenzhenPhysique}。舌诊得到的体质为：${tonguePhysique}。等症状可能是什么原因\\n 补充资料：年龄：${user.age}，性别：${user.gender}，身高：${user.height}，体重：${user.weight}，既往病史：${pastMedicalHistory || "无"}`;
				this.requestBody.messages[1].content = content
			  // 登录逻辑
			  uni.request({
			    url: 'http://ggrs.ltd:8088/v1/chat/completions',
			    method: 'POST',
			    data: JSON.stringify(this.requestBody),
			    success: (res) => {
					this.content = res.data.choices[0].message.content
					// uni.setStorageSync("content",this.content)
				  // console.log(JSON.parse(res) )
			    },
			    fail: (err) => {
			      console.log('登录失败：' + err.errMsg);
			    }
			  });
			},
		}
	}
</script>

<style>
.container {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}

.icon-wrapper {
  width: 100px; /* 根据需要调整 */
  height: 100px; /* 根据需要调整 */
  border-radius: 50%; /* 设置为圆形 */
  overflow: hidden; /* 裁剪多余的部分 */
  margin: 20px 0; /* 根据需要调整外边距 */
}

.icon {
  width: 100%;
  height: 100%;
}

.title {
  font-size: 20px; /* 根据需要调整 */
  font-weight: bold; /* 加粗 */
  margin-bottom: 20px; /* 根据需要调整外边距 */
}
.information{
	font-size: 10px;
}
.content{
	padding: 20px;
}
</style>