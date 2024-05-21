<template>
	<view class="information-container">
<!-- 		<view class="input-container">
			<text class="input-label">电话：</text>
			<input class="uni-input"  @blur="checkPhone()" type="text" placeholder="请输入您的电话" v-model="user.phone" />
		</view> -->
		<view class="input-container">
			<text class="input-label">病历号：</text>
			<uni-easyinput class="input" trim="all" v-model="user.medicalRecordNumber" placeholder="请输入您的病历号(或无)" ></uni-easyinput>
		</view>
		<view class="input-container">
			<text class="input-label">身高：</text>
			<uni-easyinput class="input" trim="all" @blur="checkHeight()" v-model="user.height" placeholder="请输入您的身高/厘米" ></uni-easyinput>
		</view>
		<view class="input-container">
			<text class="input-label">体重：</text>
			<uni-easyinput class="input" trim="all" @blur="checkWeight()" v-model="user.weight" placeholder="请输入您的体重/公斤" ></uni-easyinput>
		</view>
		<view class="input-container">
			<text class="input-label">年龄：</text>
			<uni-easyinput class="input" trim="all" @blur="checkAge()" v-model="user.age" placeholder="请输入您的年龄/岁" ></uni-easyinput>
		</view>
		<view class="input-container">
			<text class="input-label">性别：</text>
			<picker  mode="selector" @blur="checkGender()" @change="bindGenderChange" :value="user.gender" :range="genders">
			<view class="uni-input">
				{{genders[user.genderIndex]}}
			</view>
			</picker>
		</view>	
		<view class="row3">既往病史：</view>
		<view class="form-group" v-for="(group, i) in pastMedicalHistory" :key="group.title">
		    <checkbox-group class="checkbox-group" v-model="group.values" @change="onCheckboxGroupChange(i, $event)">
		        <checkbox 
				v-for="(item, j) in group.options" 
				:label="item" 
				:key="j" 
				:value="item" 
				:checked="group.values.includes(item)" >
				{{ item }}
				</checkbox>
			</checkbox-group>
		</view>
		<view class="row3">最近症状：</view>
		<view class="input-container">
			<uni-easyinput type="textarea" autoHeight v-model="recentSymptoms" placeholder="请输入内容"></uni-easyinput>
		</view>
		<button class="submit-btn" @click="submitForm">提交</button>
		<view>
			<!-- 提示信息弹窗 -->
			<uni-popup ref="message" type="message">
				<uni-popup-message type="error" message="!!!!!!!!!!" duration="2000"></uni-popup-message>
			</uni-popup>
		</view>
		<!-- 提示窗示例 -->
		<uni-popup ref="alertDialog" type="dialog">
			<uni-popup-dialog :type="msgType" cancelText="关闭" confirmText="同意" title="通知" content="欢迎使用 uni-popup!" @confirm="dialogConfirm"
				@close="dialogClose"></uni-popup-dialog>
		</uni-popup>
	</view>	  
</template>

<script>
	export default {
		onLoad() {
			// 当页面被加载时，执行数据回显
			this.showUserInformation();
		},
		data() {
			return {
				user: {
				  // phone:'',
				  medicalRecordNumber:'',
				  height: '',
				  weight: '',
				  age: '',
				  gender: '',
				  genderIndex: 0 // 用于picker的索引
				},
				genders: ['请选择性别', '男', '女'] ,// picker的数据源	
				pastMedicalHistory: [
				    {
				        options: ['心脏疾病', '肝脏疾病', '脾胃疾病', '呼吸系统疾病', '肾脏疾病', '高血压疾病', '糖尿病', '长期服用药物', '重大手术'],
				        values: []
				    },],
				recentSymptoms:''
			}
		},
		methods: {
			checkPhoneFormat(event) {
			  // 检查电话号码格式的逻辑
			},
			bindGenderChange(e) {
			  this.user.genderIndex = e.detail.value;
			  this.user.gender = this.genders[e.detail.value];
			},
			//单选框选择
			onCheckboxGroupChange(index, event) {
			  this.pastMedicalHistory[index].values =  event.detail.value;;
			},
		    submitForm() {
		      // 1. 检查必填字段是否已填写
		      if (!this.checkHeight() || !this.checkWeight() || !this.checkAge() || this.user.genderIndex === 0) {
		        // 显示错误信息或提示用户填写
				uni.showToast({
				  title: '请选择性别',
				  icon: 'none',
				  duration: 1400
				});
		        return;
		      }
		      this.storeData();
			  uni.switchTab({
			      url: '/pages/home/wangzhen/wangzhen'
			  }); 
		    },
		    storeData() {
		      // 将用户数据存储到缓存中
			  console.log(this.pastMedicalHistory[0].values)
		      const userData = JSON.parse(JSON.stringify(this.user)); // 深拷贝用户数据
		      const pastMedicalHistory = this.pastMedicalHistory[0].values.join(','); // 深拷贝表单数据
			  const recentSymptoms = this.recentSymptoms
		      const dataToStore = {
		        user: userData,
		        pastMedicalHistory: pastMedicalHistory,
				recentSymptoms:recentSymptoms,
		      };
		      uni.setStorage({key:'userInformation', 
							  data:JSON.stringify(dataToStore)});
		    },		  
			checkPhone(){
				const phoneRegex = /^1[3-9]\d{9}$/; // 假设这是电话号码的正则表达式
				if (phoneRegex.test(this.user.phone)) {
				  // 电话号码格式错误
				  return true;
				}else{
					uni.showToast({
					  title: '请检查手机号',
					  icon: 'none',
					  duration: 1400
					});
				}
			},	  
			checkHeight(){
				const numberRegex = /^\d+$/; // 假设这是数字的正则表达式		
				if (numberRegex.test(this.user.height)) {
				  // 身高格式错误
				  return true;
				}else{
					uni.showToast({
					  title: '请检查身高',
					  icon: 'none',
					  duration: 1400
					});
					this.$refs.message.open()
				}
			},
			checkWeight(){
				const numberRegex = /^\d+$/; // 假设这是数字的正则表达式		
				if (numberRegex.test(this.user.weight)) {
				  // 体重格式错误
				  return true;
				}else{
					uni.showToast({
					  title: '请检查体重',
					  icon: 'none',
					  duration: 1400
					});
					
					this.$refs.alertDialog.open()
				}
			},	  
			checkAge(){
				const numberRegex = /^\d+$/; // 假设这是数字的正则表达式		
				if (numberRegex.test(this.user.age)) {
				  // 年龄格式错误
				  return true;
				}else{
					uni.showToast({
					  title: '请检查年龄',
					  icon: 'none',
					  duration: 1400
					});
				}
			},
			checkGender(){
				if (!this.user.gender===0) {
				  // 性别格式错误
				  return true;
				}else{
					uni.showToast({
					  title: '请选择性别',
					  icon: 'none',
					  duration: 1400
					});
				}
			},	
					  
					  
			
			showUserInformation() {
				uni.getStorage({
					key: 'userInformation',
					success: (res) =>  {
						const userInfo = JSON.parse(res.data)
						// this.user.phone = userInfo.user.phone;
						this.user.medicalRecordNumber = userInfo.user.medicalRecordNumber;
						this.user.height = userInfo.user.height;
						this.user.weight = userInfo.user.weight;
						this.user.age = userInfo.user.age;
						this.user.gender = userInfo.user.gender;
						this.user.genderIndex = this.genders.indexOf(this.user.gender);
						
						this.pastMedicalHistory[0].values = userInfo.pastMedicalHistory.split(',').map(disease => disease.trim());
						this.recentSymptoms = userInfo.recentSymptoms
									
					},fail: (err) =>  {
						console.log(err)
					}
				});
			},
				  
		}
	}
</script>

<style>
	.information-container{
		padding-top: 3%;
		font-size: 20px; /* 根据需要调整字体大小 */
		margin: 10px;
		border-radius: 8px;
		box-shadow: 0 4px 10px rgba(0,0,0,0.15);
		background-color: #ffffff;
	}
	.input-container {
		display: flex;
		align-items: center; /* 垂直居中对齐 */
		margin-bottom: 10px; /* 根据需要调整间距 */
		padding-bottom: 20px;
		margin: 20px;
	}
	.input-label {
		margin-right: 8px; /* 根据需要调整间距 */
		font-size: 16px; /* 根据需要调整字体大小 */
	}
	input[type="text"] {
		/* 输入框的样式 */
		font-size: 16px; /* 根据需要调整字体大小 */
		padding: 8px; /* 根据需要调整内边距 */
		border: 1px solid #ccc; /* 根据需要调整边框样式 */

	}
	input[type="text"]:focus {
	  outline: none;
	  border-color: #007BFF;
	  box-shadow: 0 0 0 2px rgba(0,123,255,.25);
	}
	.row3{
		padding-top: 10px;
		margin: 10px;
	}
	.form-group{
		padding-bottom: 20px;
		margin: 10px;
	}
	checkbox{
		padding: 10px;
	}
	.submit-btn{
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
	.submit-btn:hover {
	  background-color: #0056b3;
	}
	
	.submit-btn:active {
	  background-color: #00408b;
	}
	
	.submit-btn:focus {
	  box-shadow: 0 0 0 2px rgba(0,123,255,.5);
	}
</style>
