<template>
	<view class="container2">
		<view class="row3">患者编号:{{patientIdcard}}</view>
		<view class="image">
			<image class="img" :src="imgUrl" mode="aspectFit" @click="openPreview"></image>
		</view>	
		<view class="row3">舌部特征</view>
		<view class="row2">
		  <view class="cell">全舌</view>
		  <view class="cell">舌中</view>
		  <view class="cell">舌根</view>
		  <view class="cell">舌左</view>
		  <view class="cell">舌右</view>
		  <view class="cell">舌尖</view>
		</view>
		
		<view class="container">
		<block v-for="item in pickers" :key="item.id">
			<picker mode="multiSelector" @change="pick" :data-index="item.id" :value="item.index" :range="ranges[item.id]" >
			<view class="flex-container">
				<view class="item">{{ item.name }}</view>
				<!-- <view class="spaced-text">{{ item.selectedValue }}</view> -->
				<view class="spaced-text">
					<text class="word" v-for="(word, index) in item.selectedValue.split(' ')" :key="index">{{ word }}</text>
				</view>
			</view>
			</picker>
		</block>
		</view>
		<view class="row">
		  <view class="remark_cell">备注：</view>
		  <input class="remark_input" v-model="remark1" type="text" placeholder="请输入备注" />
		</view>
		<view class="row3">诊断结果</view>
		<view class="form-group" v-for="(group, i) in formData" :key="group.title">
		    <view class="group-title">{{ group.title }}</view>
		    <checkbox-group class="checkbox-group" v-model="group.values" @change="onCheckboxGroupChange(i, $event)">
		        <checkbox v-for="(item, j) in group.options" :label="item" :key="j" :value="item" >
				{{ item }}
				</checkbox>
			</checkbox-group>
		</view>
		
		<view class="row">
		  <view class="remark_cell">备注：</view>
		  <input class="remark_input" v-model="remark2" type="text" placeholder="请输入备注" />
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
				remark1: "",
				remark2: "",

				// 在此处初始化和定义ranges数组
					ranges: [
					   [['粉红', '红',  '深红', '绛', '淡', '白','青', '青紫', '紫暗','暗'],
					   ['粉红', '红',  '深红', '绛', '淡', '白','青', '青紫', '紫暗','暗'],
					   ['粉红', '红',  '深红', '绛', '淡', '白','青', '青紫', '紫暗','暗'],
					   ['粉红', '红',  '深红', '绛', '淡', '白','青', '青紫', '紫暗','暗'],
					   ['粉红', '红',  '深红', '绛', '淡', '白','青', '青紫', '紫暗','暗'],
					   ['粉红', '红',  '深红', '绛', '淡', '白','青', '青紫', '紫暗','暗']],
					   
					   
					   [['正常', '胖大', '瘦薄', '齿痕', '干裂', '嫩舌', '老舌', '歪斜', '缩短'],
					   ['正常', '胖大', '瘦薄', '齿痕', '干裂', '嫩舌', '老舌', '歪斜', '缩短'],
					   ['正常', '胖大', '瘦薄', '齿痕', '干裂', '嫩舌', '老舌', '歪斜', '缩短'],
					   ['正常', '胖大', '瘦薄', '齿痕', '干裂', '嫩舌', '老舌', '歪斜', '缩短'],
					   ['正常', '胖大', '瘦薄', '齿痕', '干裂', '嫩舌', '老舌', '歪斜', '缩短'],
					   ['正常', '胖大', '瘦薄', '齿痕', '干裂', '嫩舌', '老舌', '歪斜', '缩短']],
					   
					   [['白', '黄', '灰', '黑'],
					   ['白', '黄', '灰', '黑'],
					   ['白', '黄', '灰', '黑'],
					   ['白', '黄', '灰', '黑'],
					   ['白', '黄', '灰', '黑'],
					   ['白', '黄', '灰', '黑']],
					   
					   [['薄苔', '少苔','无苔','厚苔','腻苔','腐苔','燥苔','少津','润苔'],
					   ['薄苔', '少苔','无苔','厚苔','腻苔','腐苔','燥苔','少津','润苔'],
					   ['薄苔', '少苔','无苔','厚苔','腻苔','腐苔','燥苔','少津','润苔'],
					   ['薄苔', '少苔','无苔','厚苔','腻苔','腐苔','燥苔','少津','润苔'],
					   ['薄苔', '少苔','无苔','厚苔','腻苔','腐苔','燥苔','少津','润苔'],
					   ['薄苔', '少苔','无苔','厚苔','腻苔','腐苔','燥苔','少津','润苔']],
					   
					   [['正常', '全苔', '偏苔', '剥苔'],
					   ['正常', '全苔', '偏苔', '剥苔'],
					   ['正常', '全苔', '偏苔', '剥苔'],
					   ['正常', '全苔', '偏苔', '剥苔'],
					   ['正常', '全苔', '偏苔', '剥苔'],
					   ['正常', '全苔', '偏苔', '剥苔'],],
					],
					
					pickers: [
					   {id:0, name: "舌色", index: [0, 0, 0, 0, 0, 0], selectedValue: '' },
					   {id:1, name: "舌形", index: [0, 0, 0, 0, 0, 0], selectedValue: '' },
					   {id:2, name: "苔色", index: [0, 0, 0, 0, 0, 0], selectedValue: ''  },
					   {id:3, name: "苔质", index: [0, 0, 0, 0, 0, 0], selectedValue: ''},
					   {id:4, name: "舌苔分布", index: [0, 0, 0, 0, 0, 0], selectedValue: '' },],
					   
					   
				formData: [
				    {
				        title: '心',
				        options: ['心火', '心气虚', '新血虚', '心阴虚', '心阳虚','心血瘀阻', '痰瘀阻滞'],
				        values: []
				    },
					{
					    title: '肺',
					    options: ['肺热','肺气盛', '肺阴虚',  '痰湿阻肺'],
					    values: []
					},
				    {
				        title: '肝胆',
				        options: ['肝热','肝血虚','肝阴虚', '肝血瘀阻','肝胆湿热' ],
				        values: []
				    },
					{
					    title: '脾胃',
					    options: ['脾气虚', '脾阳虚','胃阴虚','胃热','湿阻中焦','中焦湿热','食积'],
					    values: []
					},
				    {
				        title: '肾',
				        options: ['肾阴虚', '湿阻下焦', '下焦湿热'],
				        values: []
				    },
				    {
				        title: '体质',
				        options: ['平和质', '特禀质', '气虚质', '阳虚质', '阴虚质', '气郁质', '淤血质', '痰湿质', '湿热质'],
				        values: []
				    },
					
				   
				],	   
			}
		},
		
		onLoad() {
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
			
			//保存多选下拉框的值
			pick(event) {
			  const values = event.detail.value;
			  const pickerIndex = event.currentTarget.dataset.index;

			  let updatedPickers = this.pickers.map((picker, index) => {
			    if (index === pickerIndex) {
			      return { ...picker, index: values, selectedValue: `${this.ranges[pickerIndex][0][values[0]]} ${this.ranges[pickerIndex][0][values[1]]} ${this.ranges[pickerIndex][0][values[2]]} ${this.ranges[pickerIndex][0][values[3]]} ${this.ranges[pickerIndex][0][values[4]]} ${this.ranges[pickerIndex][0][values[5]]}` };
			    } else {
			      return picker;
			    }
			  });
			  this.pickers = updatedPickers; // 将更新后的pickers赋值给data中对应的属性名
			  console.log(this.pickers)
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
				data["remark1"]=this.remark1;
				data["remark2"]=this.remark2;
				data["imageID"]=this.imageID
				this.formData.forEach(item => {
				    data[item.title] = item.values;
				});
				//整合数据
				this.pickers.forEach(item => {
				    data[item.name] = item.selectedValue;
				});
				//整合数据
				data["心_All"]="";
				data["肺_All"]="";
				data["肝胆_All"]="";
				data["脾胃_All"]="";
				data["肾_All"]="";
				data["体质_All"]="";
				data["心"].forEach(item => {
					console.log(item);
				    data["心_All"] += item+"/";
				});
				data["肺"].forEach(item => {
					console.log(item);
				    data["肺_All"] += item+"/";
				});
				data["肝胆"].forEach(item => {
					console.log(item);
				    data["肝胆_All"] += item+"/";
				});
				data["脾胃"].forEach(item => {
					console.log(item);
				    data["脾胃_All"] += item+"/";
				});
				data["肾"].forEach(item => {
					console.log(item);
				    data["肾_All"] += item+"/";
				});
				data["体质"].forEach(item => {
					console.log(item);
				    data["体质_All"] += item+"/";
				});
				
			
				//发送请求
				uni.request({
			        url: 'http://localhost:9003/shezhen/update',
			        method: 'POST', // 根据实际情况设置请求方法，如GET、POST等
					header:{"Content-Type": "application/json",
							"token": uni.getStorageSync('token')
					},
			        data: { 
					  imageID: data["imageID"],
					  tongueColor:data["舌色"],
					  tongueShape:data["舌形"],
					  moss:data["苔色"],
					  lichen:data["苔质"],
					  tongueMossDistribution:data["舌苔分布"],
					  tongueFeaturesRemarks:data["remark1"],
					  heart:data["心_All"],
					  lungs:data["肺_All"],
					  liverAndGallbladder:data["肝胆_All"],
					  spleenAndStomach:data["脾胃_All"],
					  kidneys:data["肾_All"],
					  constitution:data["体质_All"],
					  diagnosticResultsRemarks:data["remark2"],
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
			    url: 'http://localhost:9003/shezhen/getFirstNotDone',
			    method: 'GET',
			    success: (res) => {
					if(res.statusCode >= 200 && res.statusCode < 300){
			              // 请求成功，处理返回的数据
				  	      this.patientIdcard=res.data.user.mobile
				  	      this.imgUrl=res.data.user.imgUrl
				  	      this.imageID= res.data.user.imageID
			              // 更新页面数据或者其他操作
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
			      console.error(error)
			    }
			  })
			},
			
			//刷新页面
			refreshPage() {
			      uni.redirectTo({
						url: '/pages/doctor/wangzhen/tongue' // 替换成当前页面路径
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
	width: 100rpx;
}
.remark_input{
	width: 650rpx;
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
  flex-wrap: wrap;
  margin-bottom: 1rpx;
  border: 1px solid #000;
}
.spaced-text {
  display: flex;
  justify-content: space-between;
  flex-wrap: wrap;
 width: 550rpx; /* 设置最大宽度为100% */
}

.item {
  width: 80rpx;
  margin-right:20rpx;
  word-spacing: 15rpx;
  text-align: right;
  border-right: 1px solid #000;
}

.row2 {
  display: flex;
  align-items: center;
  justify-content: space-between;
  margin-bottom: -30rpx;
  margin-top: -55rpx;
  padding-left: 150rpx;
  padding-right: 15rpx;
  font-weight: bold;
}
.row3{
	font-weight: bold;
	margin-bottom: 20rpx;
}

</style>
