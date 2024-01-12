<template>
    <view class="container">
		<view class="row3">患者编号:{{patientIdcard}}</view>
		<view class="image-container">
			<image class="img" :src="imgUrl" mode="aspectFit" @click="openPreview"></image>
			<view class="label">已标注</view>
		</view>	
		<view class="row3">面部特征</view>
		<view class="row2 header">
		  <view class="cell">色</view>
		  <view class="cell">偏向</view>
		  <view class="cell">泽</view>
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
		  <input disabled="ture" class="remark_input" v-model="remark1" type="text"  />
		</view>
		<view class="row3">诊断结果</view>
        <view class="form-group" v-for="(group, i) in formData" :key="group.title">
            <view class="group-title">{{ group.title }}</view>
            <checkbox-group class="checkbox-group" v-model="group.values" @change="onCheckboxGroupChange(i, $event)">
                <checkbox 
				v-for="(item, j) in group.options" 
				:label="item" :key="j" 
				:value="item" 
				:checked="group.values.includes(item)"
				disabled="true">
				{{ item }}
				</checkbox>
			</checkbox-group>
        </view>

		<view class="row">
		  <view class="remark_cell">备注：</view>
		  <input disabled="ture" class="remark_input" v-model="remark2" type="text"  />
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
			remark1: "",
			remark2: "",
            formData: [
                {
                    title: '心',
                    options: ['心气虚', '心阳虚', '新血虚', '心阴虚', '心血瘀阻', '心火上炎'],
                    values: []
                },
                {
                    title: '肝胆',
                    options: ['肝气郁结', '肝风内动', '肝阴虚', '肝阳上亢', '肝血瘀阻', '寒湿阻滞', '肝胆湿热', '肝血虚'],
                    values: []
                },
                {
                    title: '肾',
                    options: ['肾阳虚', '肾阴虚', '膀胱湿热', '肾气虚'],
                    values: []
                },
                {
                    title: '肠胃',
                    options: ['大肠湿热', '胃寒', '胃热', '胃阴虚'],
                    values: []
                },
                {
                    title: '脾',
                    options: ['脾气虚', '脾阳虚', '寒湿困脾'],
                    values: []
                },
                {
                    title: '肺',
                    options: ['肺气盛', '肺阴虚', '肺热', '痰湿阻肺'],
                    values: []
                }
            ],
		// 在此处初始化和定义ranges数组
			ranges: [
			   ['红', '黄', '白', '青', '黑', '紫', '褐', '粉'],
			   ['淡', '深', '紫', '灰', '红', '黄', '白', '青', '黑'],
			   ['明', '暗']
			],
			
			pickers: [
			   { name: "整体", index: [0, 0, 0], selectedValue: '' },
			   { name: "颌", index: [0, 0, 0], selectedValue: '' },
			   { name: "唇", index: [0, 0, 0], selectedValue: ''  },
			   { name: "鼻根", index: [0, 0, 0], selectedValue: ''},
			   { name: "鼻中", index: [0, 0, 0], selectedValue: '' },
			   { name: "鼻两侧", index: [0, 0, 0], selectedValue: ''} ,
			   { name: "鼻头", index: [0, 0, 0], selectedValue: '' },
			   { name: "鼻翼", index: [0, 0, 0], selectedValue: '' },
			   { name: "眼部", index: [0, 0, 0], selectedValue: '' },
			   { name: "眉部", index: [0, 0, 0], selectedValue: '' },
			   { name: "颊", index: [0, 0, 0], selectedValue: '' },
			   { name: "上额", index: [0, 0, 0], selectedValue: '' },
			   { name: "前额", index: [0, 0, 0], selectedValue: '' },
			   { name: "颞", index: [0, 0, 0], selectedValue: ''},
			    ],

        }
    },
	
	  onLoad() {
         //获取全局变量 赋值后处理业务逻辑
	    // 在页面加载时调用后端接口请求数据
	    this.fetchData()
	  },

    methods: {
		//单选框选择
        onCheckboxGroupChange(i, e) {
            this.formData[i].values = e.detail.value;
        },
		//放大图片
		openPreview() {
		  uni.previewImage({
		    current: this.imgUrl, // 当前显示的图片链接
		    urls: [this.imgUrl] // 需要预览的图片链接列表（可添加多张）
		  })
		},
		
		//上一个
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
		//下一个
		next(){
			this.offset++
			this.fetchData()
			console.log(this.offset)
		},
		 //获取第一个未标注的数据 
		 fetchData() {
		   // 发送HTTP请求到后端接口
		   uni.request({
		     url: 'http://121.41.80.48:9003/wangzhen/getDone',
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
			         this.patientIdcard=res.data.Idcard
					 this.imgUrl =res.data.user[0].imgUrl
			         this.imageID= res.data.user[0].imageID
					 //pickers
					 this.pickers[0].selectedValue = res.data.user[0].whole
					 this.pickers[1].selectedValue = res.data.user[0].jaw
					 this.pickers[2].selectedValue = res.data.user[0].lip
					 this.pickers[3].selectedValue = res.data.user[0].nasalRoot
					 this.pickers[4].selectedValue = res.data.user[0].nasalBridge
					 this.pickers[5].selectedValue = res.data.user[0].bothSidesOfTheNose
					 this.pickers[6].selectedValue = res.data.user[0].noseHead
					 this.pickers[7].selectedValue = res.data.user[0].nasalAlar
					 this.pickers[8].selectedValue = res.data.user[0].eyes
					 this.pickers[9].selectedValue = res.data.user[0].eyebrowRegion
					 this.pickers[10].selectedValue = res.data.user[0].cheek
					 this.pickers[11].selectedValue = res.data.user[0].maxilla
					 this.pickers[12].selectedValue = res.data.user[0].forehead
					 this.pickers[13].selectedValue = res.data.user[0].temporal
					 this.remark1 = res.data.user[0].facialFeaturesRemarks
					 try{
						 //checkbox
					 	this.formData[0].values = res.data.user[0].heart.split('/').filter(Boolean);
					 	this.formData[1].values = res.data.user[0].liverAndGallbladder.split('/').filter(Boolean);
					 	this.formData[2].values = res.data.user[0].kidney.split('/').filter(Boolean);
					 	this.formData[3].values = res.data.user[0].gastroenterology.split('/').filter(Boolean);
					 	this.formData[4].values = res.data.user[0].spleen.split('/').filter(Boolean);
					 	this.formData[5].values = res.data.user[0].lung.split('/').filter(Boolean);
					 }catch(e){
						 uni.showToast({
						  title: '出错了',
						  icon: 'none',
						  duration: 4000
						 });
					 	//TODO handle the exception
					 }
					 this.remark2 = res.data.user[0].diagnosticResultsRemarks
		             // 更新页面数据或者其他操作
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
				uni.showToast({
				  title: '没有数据了',
				  icon: 'none',
				  duration: 2000
				}); 
		       // 请求失败，处理错误信息
		       console.error(error)
		     }
		   })
		 },
		 
		// //保存多选下拉框的值
		// pick(event) {
		//   const values = event.detail.value;
		//   const pickerIndex = event.currentTarget.dataset.index;
		  
		//   let updatedPickers = this.pickers.map((picker, index) => {
		//     if (index === pickerIndex) {
		//       return { ...picker, index: values, selectedValue: `${this.ranges[0][values[0]]} ${this.ranges[1][values[1]]} ${this.ranges[2][values[2]]}` };
		//     } else {
		//       return picker;
		//     }
		//   });
		//   this.pickers = updatedPickers; // 将更新后的pickers赋值给data中对应的属性名
		// },
		
		// //点击保存按钮，弹出提示框
		// onSaveClick() {
		//   uni.showModal({
		//     title: '提示',
		//     content: '是否要保存？结果无法更改',
		//     success: (res) => {
		//       if (res.confirm) { // 用户点击了确定按钮
		//         this.saveData();
		//       } else if (res.cancel) { // 用户点击了取消按钮
		        
		//       }
		//     }
		//   });
		// },
		// //点击下一页按钮，弹出提示框
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
		// // 点击保存按钮，将数据传到后台
		// saveData() {
		// 	let data = {}
		// 	data["remark1"]=this.remark1;
		// 	data["remark2"]=this.remark2;
		// 	data["imageID"]=this.imageID
			
		// 	this.formData.forEach(item => {
		// 	    data[item.title] = item.values;
		// 	});
		// 	//整合数据
		// 	this.pickers.forEach(item => {
		// 	    data[item.name] = item.selectedValue;
		// 	});
		// 	//整合数据
		// 	data["心_All"]="";
		// 	data["肝胆_All"]="";
		// 	data["肾_All"]="";
		// 	data["肠胃_All"]="";
		// 	data["脾_All"]="";
		// 	data["肺_All"]="";
		// 	data["心"].forEach(item => {
		// 		console.log(item);
		// 	    data["心_All"] += item+"/";
		// 	});
		// 	data["肝胆"].forEach(item => {
		// 		console.log(item);
		// 	    data["肝胆_All"] += item+"/";
		// 	});
		// 	data["肾"].forEach(item => {
		// 		console.log(item);
		// 	    data["肾_All"] += item+"/";
		// 	});
		// 	data["肠胃"].forEach(item => {
		// 		console.log(item);
		// 	    data["肠胃_All"] += item+"/";
		// 	});
		// 	data["脾"].forEach(item => {
		// 		console.log(item);
		// 	    data["脾_All"] += item+"/";
		// 	});
		// 	data["肺"].forEach(item => {
		// 		console.log(item);
		// 	    data["肺_All"] += item+"/";
		// 	});
			
		
			
		// 	//发送请求
		// 	uni.request({
		//         url: 'http://121.41.80.48:9003/wangzhen/update',
		//         method: 'POST', // 根据实际情况设置请求方法，如GET、POST等
		// 		header:{"Content-Type": "application/json",
		// 				"token": uni.getStorageSync('token')
		// 		},
		//         data: { 
		//           patientIdcard: data["patientIdcard"],
		// 		  imageID: data["imageID"],
		// 		  whole:data["整体"],
		// 		  jaw:data["颌"],
		// 		  lip:data["唇"],
		// 		  nasalRoot:data["鼻根"],
		// 		  nasalBridge:data["鼻中"],
		// 		  bothSidesOfTheNose:data["鼻两侧"],
		// 		  noseHead:data["鼻头"],
		// 		  nasalAlar:data["鼻翼"],
		// 		  eyes:data["眼部"],
		// 		  eyebrowRegion:data["眉部"],
		// 		  cheek:data["颊"],
		// 		  maxilla:data["上额"],
		// 		  forehead:data["前额"],
		// 		  temporal:data["颞"],
		// 		  facialFeaturesRemarks:data["remark1"],
		// 		  heart:data["心_All"],
		// 		  liverAndGallbladder:data["肝胆_All"],
		// 		  kidney:data["肾_All"],
		// 		  gastroenterology:data["肠胃_All"],
		// 		  spleen:data["脾_All"],
		// 		  lung:data["肺_All"],
		// 		  diagnosticResultsRemarks:data["remark2"],
		// 		  done:1,
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
		 
		 // //刷新页面
		 // refreshPage() {
		 //       uni.redirectTo({
			// 			url: '/pages/list/wangzhen/list' // 替换成当前页面路径
			// 		});
			// 	}   
		 
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
    padding: 20px;
}
.form-group {
    margin-bottom: 20px;
}
.group-title {
    font-size: 18px;
    color: #333;
    margin-bottom: 10px;
}
.checkbox-group {
    display: flex;
    flex-wrap: wrap;
}
.checkbox-group checkbox {
    margin-right: 20px;
    margin-bottom: 10px;
}
.submit-btn {
    text-align: center;
    background-color: #1aad19;
    color: #fff;
    padding: 10px 0;
    border-radius: 5px;
}

.img{
	width: 750rpx;
	height: 750rpx;
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

.cell {
  flex: 1;
}
.remark_cell{
	width: 130rpx;
}
.remark_input{
	width: 620rpx;
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
.flex-container {
  display: flex;
  padding: 10rpx;
  margin-bottom: 1rpx;
  border: 1px solid #000;
}
.item {
  width: 150rpx;
  margin-right:60rpx;
  word-spacing: 15rpx;
  text-align: right;
}
.spaced-text {
  width: 400rpx;
  margin-right:50rpx;
  word-spacing: 85rpx; /* 调整空格大小 */
  text-align: right;
  border-left: 1px solid #000;
}
.row2 {
  display: flex;
  align-items: center;
  justify-content: space-between;
  margin-bottom: -30rpx;
  margin-top: -30rpx;
  padding-left: 275rpx;
  padding-right: 10rpx;
  font-weight: bold;
}
.row3{
	font-weight: bold;
	margin-bottom: 20rpx;
}

</style>
