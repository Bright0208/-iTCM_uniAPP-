<template>
    <view class="container">
		<view class="row3">患者编号:{{patientIdcard}}</view>
		<view class="image">
			<image class="img" :src="imgUrl" mode="aspectFit" @click="openPreview"></image>
		</view>	
		<view class="row3">面部特征</view>
		<view class="row2 header">
		  <view class="cell">色</view>
		  <view class="cell">偏向</view>
		  <view class="cell">泽</view>
		</view>
		
		<view class="container">
		<block v-for="item in pickers" :key="item.id">
			<picker mode="multiSelector" @change="pick" :data-index="item.id" :value="item.index" :range="ranges">
			<view class="flex-container">
				<view class="item">{{ item.name }}</view>
				<view class="spaced-text">{{ item.selectedValue }}</view>
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
			   {id:0, name: "整体", index: [0, 0, 0], selectedValue: '' },
			   {id:1, name: "颌", index: [0, 0, 0], selectedValue: '' },
			   {id:2, name: "唇", index: [0, 0, 0], selectedValue: ''  },
			   {id:3, name: "鼻根", index: [0, 0, 0], selectedValue: ''},
			   {id:4, name: "鼻中", index: [0, 0, 0], selectedValue: '' },
			   {id:5, name: "鼻两侧", index: [0, 0, 0], selectedValue: ''} ,
			   {id:6, name: "鼻头", index: [0, 0, 0], selectedValue: '' },
			   {id:7, name: "鼻翼", index: [0, 0, 0], selectedValue: '' },
			   {id:8, name: "眼部", index: [0, 0, 0], selectedValue: '' },
			   {id:9, name: "眉部", index: [0, 0, 0], selectedValue: '' },
			   {id:10, name: "颊", index: [0, 0, 0], selectedValue: '' },
			   {id:11, name: "上额", index: [0, 0, 0], selectedValue: '' },
			   {id:12, name: "前额", index: [0, 0, 0], selectedValue: '' },
			   {id:13, name: "颞", index: [0, 0, 0], selectedValue: ''},
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
			data["肝胆_All"]="";
			data["肾_All"]="";
			data["肠胃_All"]="";
			data["脾_All"]="";
			data["肺_All"]="";
			data["心"].forEach(item => {
				console.log(item);
			    data["心_All"] += item+"/";
			});
			data["肝胆"].forEach(item => {
				console.log(item);
			    data["肝胆_All"] += item+"/";
			});
			data["肾"].forEach(item => {
				console.log(item);
			    data["肾_All"] += item+"/";
			});
			data["肠胃"].forEach(item => {
				console.log(item);
			    data["肠胃_All"] += item+"/";
			});
			data["脾"].forEach(item => {
				console.log(item);
			    data["脾_All"] += item+"/";
			});
			data["肺"].forEach(item => {
				console.log(item);
			    data["肺_All"] += item+"/";
			});
			
			
			//发送请求
			uni.request({
		        url: 'http://localhost:9003/wangzhen/update',
		        method: 'POST', // 根据实际情况设置请求方法，如GET、POST等
				header:{"Content-Type": "application/json",
						"token": uni.getStorageSync('token')
				},
		        data: { 
		          patientIdcard: data["patientIdcard"],
				  imageID: data["imageID"],
				  whole:data["整体"],
				  jaw:data["颌"],
				  lip:data["唇"],
				  nasalRoot:data["鼻根"],
				  nasalBridge:data["鼻中"],
				  bothSidesOfTheNose:data["鼻两侧"],
				  noseHead:data["鼻头"],
				  nasalAlar:data["鼻翼"],
				  eyes:data["眼部"],
				  eyebrowRegion:data["眉部"],
				  cheek:data["颊"],
				  maxilla:data["上额"],
				  forehead:data["前额"],
				  temporal:data["颞"],
				  facialFeaturesRemarks:data["remark1"],
				  heart:data["心_All"],
				  liverAndGallbladder:data["肝胆_All"],
				  kidney:data["肾_All"],
				  gastroenterology:data["肠胃_All"],
				  spleen:data["脾_All"],
				  lung:data["肺_All"],
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
		     url: 'http://localhost:9003/wangzhen/getFirstNotDone',
		     method: 'GET',
		     success: (res) => {
				if(res.statusCode >= 200 && res.statusCode < 300){
		             // 请求成功，处理返回的数据
			         this.patientIdcard=res.data.user.mobile
			         console.log(res.data);
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
						url: '/pages/doctor/wangzhen/face' // 替换成当前页面路径
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
