<template>
    <view class="container">
		<view class="row3">患者编号:{{patientIdcard}}</view>
		<view class="image-container" v-for="(item,index) in user" >
			<text>提交时间{{item.gmtCreate}}</text>
			<image class="img" :src="item.imgUrl" mode="aspectFit" @click="openPreview"></image>
		</view>
    </view>
</template>

<script>
export default {
    data() {
        return {
			patientIdcard:'',
			user:[]
        }
    },
	
	  onLoad() {
         //获取全局变量 赋值后处理业务逻辑
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
		 //获取第一个未标注的数据 
		 fetchData() {
			 console.log("获取数据")
		   // 发送HTTP请求到后端接口
		   uni.request({
		     url: 'http://8.138.22.54:9001/user-ear/getAllRecord',//192.168.1.163
		     method: 'GET',
			 header:{"Content-Type": "application/json",
			 		"token": uni.getStorageSync('token')
			 },
		     success: (res) => {
				if(res.statusCode >= 200 && res.statusCode < 300){
		             // 请求成功，处理返回的数据
					 console.log(res.data.user)
					 try{
						 this.user = res.data.user
						 this.patientIdcard = res.data.user[0].mobile
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
