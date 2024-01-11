<template>
	<view class="container">
		<view class="row3">患者编号:{{ patientIdcard }}</view>
		<view class="image">
			<image class="img" :src="imgUrl" mode="aspectFit" @click="openPreview"></image>
		</view>	
		
		<view class="flex-container2">
			<view>整体(基色)</view>
			<picker mode="selector" :range="options" @change="onPickerChange">
	        <view class="spaced-text2">{{ selectedOption }}</view>
			</picker>	
		</view>
		
		<view class="row2">
		  <view class="cell">颜色</view>
		  <view class="cell">颜色程度</view>
		  <view class="cell">症状程度</view>
		</view>
		
		<view class="container">
		<block v-for="(item, index) in pickers" :key="index">
			<picker mode="multiSelector" @change="pick" :data-index="index" :value="item.index" :range="ranges">
			<view class="flex-container">
				<view class="item">{{ item.name }}</view>
				<view class="spaced-text">{{ item.selectedValue }}</view>
			</view>
			</picker>
		</block>
		</view>
		
		<view class="row">
		  <view class="remark_cell">备注：</view>
		  <input class="remark_input" v-model="remark" type="text" placeholder="请输入备注" />
		</view>
		<view class="button-container">
		    <button class="half-width-button save-button" @click="onSaveClick">保存</button>
		    <button class="half-width-button next-button" @click="onNextClick">下一页</button>
		  </view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				patientIdcard:"",
				imageID:"",
				imgUrl:"",
				remark: "",
				options: ['红', '黄', '白', '青', '黑'], // 下拉框选项数组
				selectedOption: '请选择', // 当前选择的值
			// 在此处初始化和定义ranges数组
				ranges: [
				   ['红', '黄', '白', '青', '黑'],
				   ['浅', '中', '深'],
				   ['浅', '中', '深']
				],
				
				pickers: [
				   { name: "肾", index: [0, 0, 0], selectedValue: '' },
				   { name: "肝胆", index: [0, 0, 0], selectedValue: '' },
				   { name: "脾胃", index: [0, 0, 0], selectedValue: ''  },
				   { name: "肺", index: [0, 0, 0], selectedValue: ''},
				   { name: "心", index: [0, 0, 0], selectedValue: '' },
				    ],
			
				
			}
		},
		onLoad() {
		  // 在页面加载时调用后端接口请求数据
		  this.fetchData()
		},
		
		methods: {
			//放大图片
			openPreview() {
			  uni.previewImage({
			    current: this.imgUrl, // 当前显示的图片链接
			    urls: [this.imgUrl] // 需要预览的图片链接列表（可添加多张）
			  })
			},
			
			onPickerChange(e) {
			  const index = e.detail.value;
			  this.selectedOption = this.options[index];
			  
			  // 在此处执行其他逻辑操作...
			},
			
			//保存多选下拉框的值
			pick(event) {
			  const values = event.detail.value;
			  const pickerIndex = event.currentTarget.dataset.index;
			  
			  let updatedPickers = this.pickers.map((picker, index) => {
			    if (index === pickerIndex) {
			      return { ...picker, index: values, selectedValue: `${this.ranges[0][values[0]]} ${this.ranges[1][values[1]]} ${this.ranges[2][values[2]]}` };
			    } else {
			      return picker;
			    }
			  });
			  
			  this.pickers = updatedPickers; // 将更新后的pickers赋值给data中对应的属性名
			  
			},
			//点击保存按钮，弹出提示框
			onSaveClick() {
			  uni.showModal({
			    title: '提示',
			    content: '是否要保存？结果无法更改',
			    success: (res) => {
			      if (res.confirm) { // 用户点击了确定按钮
			        this.saveData();
			      } else if (res.cancel) { // 用户点击了取消按钮
			        
			      }
			    }
			  });
			},
			//点击下一页按钮，弹出提示框
			onNextClick() {
			  uni.showModal({
			    title: '提示',
			    content: '是否已保存？进入下一位',
			    success: (res) => {
			      if (res.confirm) { // 用户点击了确定按钮
			        this.refreshPage();
			      } else if (res.cancel) { // 用户点击了取消按钮
			        
			      }
			    }
			  });
			},
			
			// 点击保存按钮，将数据传到后台
			saveData() {
				let data = {}
				data["remark"]=this.remark;
				data["selectedOption"]=this.selectedOption;
				data["imageID"]=this.imageID
				//整合数据
				this.pickers.forEach(item => {
				    data[item.name] = item.selectedValue;
				});
				//整合数据
				
				console.log(data);  
				//发送请求
				uni.request({
			        url: 'http://localhost:9003/erzhen/update',
			        method: 'POST', // 根据实际情况设置请求方法，如GET、POST等
					header:{"Content-Type": "application/json",
							"token": uni.getStorageSync('token')
					},
			        data: {
					  token:uni.getStorageSync('token'),
					  imageID: data["imageID"],
					  whole:data["selectedOption"],
					  kidneys:data["肾"],
					  liverAndGallbladder:data["肝胆"],
					  spleenAndStomach:data["脾胃"],
					  lungs:data["肺"],
					  heart:data["心"],
					  diagnosticResultsRemarks:data["remark"],
			          done:1,
			        },
			        success(res) {
					  uni.showToast({
					    title: '保存成功',
					    icon: 'success'
					  });
			          console.log('发送数据成功', res.data);
			          // 处理返回的响应结果
			        },
			        fail(err) {
			          console.error('发送数据失败', err);
			          // 处理错误情况
			        }
			      });
			
			  },
			
			
			
			
			//获取第一个未标注的数据
			fetchData() {
			  // 发送HTTP请求到后端接口
			  uni.request({
			    url: 'http://localhost:9003/erzhen/getFirstNotDone',
			    method: 'GET',
			    success: (res) => {
				  if(res.statusCode >= 200 && res.statusCode < 300){
				      // 请求成功，处理返回的数据
				      console.log(res.data);
				      this.patientIdcard=res.data.Idcard
				      this.imgUrl=res.data.user.imgUrl
				      this.imageID= res.data.user.imageID
				      console.log( this.imageID);  
				   }else{
				  	 uni.showToast({
				  	  title: '没有数据了',
				  	  icon: 'none',
				  	  duration: 4000
				  	 });
				   }		
			    },
			    fail: (error) => {
			      // 请求失败，处理错误信息
				  uni.showToast({
				    title: '没有数据了',
				    icon: 'none',
				    duration: 2000
				  });
			      console.error(error)
			    }
			  })
			},
			//刷新页面
			refreshPage() {
			   uni.redirectTo({
			     url: '/pages/doctor/wangzhen/ear' // 页面3的路径
			   });
						}   
		}
	}
</script>

<style scoped lang='scss'>
.img{
	width: 750rpx;
	height: 750rpx;
}
.container {
    padding: 0px;
}
.form-group {
    margin-bottom: 20px;
}
.group-title {
    font-size: 18px;
    color: #333;
    margin-bottom: 10px;
}

.submit-btn {
    text-align: center;
    background-color: #1aad19;
    color: #fff;
    padding: 10px 0;
    border-radius: 5px;
}

.form {
  padding: 10px;
}

.row {
  display: flex;
  align-items: center;
  justify-content: space-between;
  margin-bottom: 10px;
}

.remark_cell{
	width: 120rpx;
}
.remark_input{
	width: 630rpx;
}
.cell {
  flex: 1;
}


.button {
  background-color: #0099ff;
  color: #fff;
  text-align: center;
  padding: 10px;
  border-radius: 5px;
  cursor: pointer;
}
.button-container {
  display: flex;
}

.half-width-button {
  flex: 1;
  text-align: center;
  background-color: #1aad19;
  color: #fff;
  border-radius: 5px;
}

.save-button {
  margin-right: .5rem; /* 可根据需要调整按钮之间的间距 */
  text-align: center;
  background-color: #1aad19;
  color: #fff;
  border-radius: 5px;
}
.name {
  font-weight: bold;
}

.value {
  margin-top: 5rpx;
}

.container2 {
    padding: 0px;
}
.flex-container {
  display: flex;
  padding: 1rpx;
  margin-bottom: 1rpx;
  border: 1px solid #000;
}
.flex-container2{
  display: flex;
  padding: 10rpx;
  margin-top: 30rpx;
  margin-bottom: 20rpx;
  border: 1px solid #000;
}
.item {
  width: 80rpx;
  margin-right:20rpx;
  word-spacing: 15rpx;
  text-align: right;
}
.spaced-text {
	width: 670rpx;
  word-spacing: 170rpx; /* 调整空格大小 */
  text-align: center;
  border-left: 1px solid #000;
}
.spaced-text2{
  width: 470rpx;
  word-spacing: 170rpx; /* 调整空格大小 */
  text-align: center;
  border-left: 1px solid #000;
}
.row2 {
  display: flex;
  align-items: center;
  justify-content: space-between;
  margin-bottom: 20rpx;
  margin-top: 30rpx;
  padding-left: 175rpx;
  padding-right: 15rpx;
  font-weight: bold;
}
.row3{
	font-weight: bold;
	margin-bottom: 20rpx;
}

</style>
