<template>
	<view class="container">
		<view class="row3">患者编号:{{ patientIdcard }}</view>
		<view class="image-container">
			<image class="img" :src="imgUrl" mode="aspectFit" @click="openPreview"></image>
			<view class="label">已标注</view>
		</view>	
		
		<view class="flex-container2">
			<view>整体(基色)</view>
			<picker disabled="true" mode="selector"  :range="options" @change="onPickerChange">
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
			<picker disabled="true" mode="multiSelector" @change="pick" :data-index="index" :value="item.index" :range="ranges">
			<view class="flex-container">
				<view class="item">{{ item.name }}</view>
				<view class="spaced-text">{{ item.selectedValue }}</view>
			</view>
			</picker>
		</block>
		</view>
		
		<view class="row">
		  <view class="remark_cell">备注：</view>
		  <input disabled="true" class="remark_input" v-model="remark" type="text"  />
		</view>
		<view class="button-container">
		    <button class="half-width-button save-button" @click="previous">上一个</button>
		    <button class="half-width-button next-button" @click="next">下一个</button>
		  </view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				offset:0,
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


			
	previous(){
		this.offset--
		if(this.offset<0){
			uni.showToast({
			 title: '已经到顶了',
			 icon: 'none',
			 duration: 4000
			});
			this.offset=0;
			console.log(this.offset)
		}else{
			this.fetchData()
		}
	},
	next(){
		this.offset++
		this.fetchData()
		console.log(this.offset)
	},
	
	//获取第一个标注的数据
	fetchData() {
	  // 发送HTTP请求到后端接口
	  uni.request({
	    url: 'http://8.138.22.54:9001/erzhen/getDone',
	    method: 'GET',
		header:{"Content-Type": "application/json",
				"token": uni.getStorageSync('token')
		},
		data: {
		offset: this.offset // offsetValue是您希望传递的offset参数的值
		},
	    success: (res) => {
		  if(res.statusCode >= 200 && res.statusCode < 300){
		      // 请求成功，处理返回的数据
			  //回显数据
		      this.patientIdcard = res.data.user[0].mobile
		      this.imgUrl =res.data.user[0].imgUrl
		      this.imageID = res.data.user[0].imageID
			  this.selectedOption = res.data.user[0].whole
			  this.pickers[0].selectedValue = res.data.user[0].kidneys
			  this.pickers[1].selectedValue = res.data.user[0].liverAndGallbladder
			  this.pickers[2].selectedValue = res.data.user[0].spleenAndStomach
			  this.pickers[3].selectedValue = res.data.user[0].lungs
			  this.pickers[4].selectedValue = res.data.user[0].heart
			  this.remark = res.data.user[0].diagnosticResultsRemarks
		   }else{
		  	 uni.showToast({
		  	  title: '没有数据了',
		  	  icon: 'none',
		  	  duration: 4000
		  	 });
			 this.offset--
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
	
	
 //    //点击保存按钮，弹出提示框,在这个页面里目前没用
 //    onSaveClick() {
 //      uni.showModal({
 //        title: '提示',
 //        content: '是否要保存？结果无法更改',
 //        success: (res) => {
 //          if (res.confirm) { // 用户点击了确定按钮
 //            this.saveData();
 //          } else if (res.cancel) { // 用户点击了取消按钮
            
 //          }
 //        }
 //      });
 //    },	
	// //点击下一页按钮，弹出提示框,在这个页面里目前没用
	// onNextClick() {
	//   uni.showModal({
	//     title: '提示',
	//     content: '是否已保存？进入下一位',
	//     success: (res) => {
	//       if (res.confirm) { // 用户点击了确定按钮
	//         this.refreshPage();
	//       } else if (res.cancel) { // 用户点击了取消按钮
	        
	//       }
	//     }
	//   });
	// },
	// // 点击保存按钮，将数据传到后台,在这个页面里目前没用
	// saveData() {
	// 	let data = {}
	// 	data["remark"]=this.remark;
	// 	data["selectedOption"]=this.selectedOption;
	// 	data["imageID"]=this.imageID
	// 	//整合数据
	// 	this.pickers.forEach(item => {
	// 	    data[item.name] = item.selectedValue;
	// 	});
	// 	//整合数据
		
	// 	console.log(data);  
	// 	//发送请求
	// 	uni.request({
	//         url: 'http://8.138.22.54:9003/erzhen/update',
	//         method: 'POST', // 根据实际情况设置请求方法，如GET、POST等
	// 		header:{"Content-Type": "application/json",
	// 				"token": uni.getStorageSync('token')
	// 		},
	//         data: {
	// 		  token:uni.getStorageSync('token'),
	// 		  imageID: data["imageID"],
	// 		  whole:data["selectedOption"],
	// 		  kidneys:data["肾"],
	// 		  liverAndGallbladder:data["肝胆"],
	// 		  spleenAndStomach:data["脾胃"],
	// 		  lungs:data["肺"],
	// 		  heart:data["心"],
	// 		  diagnosticResultsRemarks:data["remark"],
	//           done:1,
	//         },
	//         success(res) {
	// 		  uni.showToast({
	// 		    title: '保存成功',
	// 		    icon: 'success'
	// 		  });
	//           console.log('发送数据成功', res.data);
	//           // 处理返回的响应结果
	//         },
	//         fail(err) {
	//           console.error('发送数据失败', err);
	//           // 处理错误情况
	//         }
	//       });
	//   },
		
			
    }
}
</script>

<style scoped lang='scss'>
.image-container {
  position: relative; /* 设置相对定位 */
  display: inline-block; /* 容器以内联块的形式展示 */
}
.img{
	width: 750rpx;
	height: 750rpx;
	display: block; /* 图片块级展示 */
}
.label {
  position: absolute; /* 设置绝对定位 */
  top: 0; /* 标签距离容器顶部0 */
  right: 130rpx; /* 标签距离容器右部0 */
  background-color: red; /* 标签背景颜色 */
  color: white; /* 标签字体颜色 */
  padding: 2px; /* 标签内间距 */
  font-size: 12px; /* 字体大小 */
  border-radius: 3px; /* 边角圆滑度 */
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
