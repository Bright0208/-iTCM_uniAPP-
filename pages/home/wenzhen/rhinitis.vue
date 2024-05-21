<template>
  <view class="container">  
<!--    <view class="question-container" v-for="(question, index) in questions" :key="index">
      <view class="question-title">{{ index + 1 }}. {{ question.title }}</view>
      <radio-group class="options-container" v-model="question.answer" @change="saveResults($event, index)">
        <radio class="option" v-for="(option, optionIndex) in question.options" :key="optionIndex" :data-question-index="optionIndex" :value="option">{{ option }}</radio>
      </radio-group>
    </view> -->
	<view class="question-container" v-for="(group, i) in questions" :key="group.title">
	    <view class="question-title">{{ i + 1 }}. {{ group.title }}</view>
	    <checkbox-group class="options-container" v-model="group.values" @change="onCheckboxGroupChange(i, $event)">
	        <checkbox v-for="(item, j) in group.options" 
			:label="item" 
			:key="j" 
			:value="item" 
			:checked="group.values.includes(item)">
			{{ item }}
			</checkbox>
		</checkbox-group>
	</view>
    <view class="save-button-container">
      <button class="save-button" @click="save">提交进行诊断</button>
    </view>	
  </view>
</template>
<script>
export default {
  data() {
    return {
      questions: [
        {
          title: '有如下过敏史和家族史吗？',
          options: ['特应性皮炎', '荨麻疹', '支气管哮喘'],
          values: []
        },
        {
          title: '有如下主要症状吗？',
          options: ['眼结膜充血', '鼻痒', '喷嚏','清水样涕','连续性喷嚏','喘息'],
          values: []
        },
		{
		  title: '有如下体征吗？',
		  options: ['鼻塞', '黑眼圈（或熊猫眼）', '下眼睑存在月形皱褶','鼻梁皮肤表面出现横行皱纹'],
		  values: []
		},
		{
		  title: '有其他症状吗？',
		  options: ['搐鼻', '鼻出血', '嗅觉减退','揉鼻','揉眼'],
		  values: []
		},
      ],
      results: [],// [{"title":"您精力充沛吗? (指精神头足，乐于做事)","answer":3},{"title":"您容易疲乏吗? (指体力如何，是否稍微活动一下或做一点家务劳动就感到累)...
	  grade: [],// [9,12,13,16,10,14,13,10,15]
    };
  },
  
  

  methods: {
	//单选框选择
	onCheckboxGroupChange(i, e) {
	    this.questions[i].values = e.detail.value;
	},
	test(){
		console.log(this.questions)
		// this.results = this.questions.map((question, index) => {
		//      return {
		//        title: question.title,
		//        answer: question.values,
		//      };
		//    });
		for ( let i=0; i<this.questions.length;i++){
			this.results.push(this.questions[i].values)
		}
		console.log(this.results)   
	},
	// submit() {
	//      this.results = this.questions.map((question, index) => {
	//           return {
	//             title: question.title,
	//             answer: question.answer,
	//           };
	//         });
	// 	 let result = this.results;
	// 	this.grade[0]=result[1].answer+result[2].answer+result[3].answer+result[13].answer;
	// 	this.grade[1]=result[10].answer+result[11].answer+result[12].answer+result[28].answer;
	// 	this.grade[2]=result[9].answer+result[20].answer+result[25].answer+result[30].answer;
	// 	this.grade[3]=result[8].answer+result[15].answer+result[27].answer+result[31].answer;
	// 	this.grade[4]=result[22].answer+result[24].answer+result[26].answer+result[29].answer;
	// 	this.grade[5]=result[18].answer+result[21].answer+result[23].answer+result[32].answer;
	// 	this.grade[6]=result[4].answer+result[5].answer+result[6].answer+result[7].answer;
	// 	this.grade[7]=result[14].answer+result[16].answer+result[17].answer+result[19].answer;
	// 	this.grade[8]=result[0].answer+this.reverse(result[1].answer)+this.reverse(result[3].answer)+this.reverse(result[4].answer)+this.reverse(result[12].answer);
	
	// 	this.diagnosis(this.grade)
	// },
	// reverse(i){
	// 	if(i===1) return 5
	// 	if(i===2) return 4
	// 	if(i===3) return 3;
	// 	if(i===4) return 2;
	// 	if(i===5) return 1;
	// 	return i
	// },
	save(){
		for ( let i=0; i<this.questions.length;i++){
			this.results.push(this.questions[i].values)
		}
		const userInformation = uni.getStorageSync('userInformation');
		uni.request({
		  url: 'http://8.138.22.54:9001/wenzhen-rhinitis/save',//8.138.22.54、192.168.1.161
		  method: 'POST',
		  data:{
			  token:uni.getStorageSync('token'),
			  answer:JSON.stringify(this.results),
			  userInformation:userInformation,
		  },
		  success: (res) => {
			if(res.statusCode >= 200 && res.statusCode < 300){
				uni.showToast({
				  title: '结果已保存',
				  icon: 'none',
				  duration: 2000
				}); 
				this.results=[];//重置诊断结果
				for (let question of this.questions){
					question.values=[]
				}
				console.log(this.questions)
			}else{
				uni.showToast({
				  title: '结果保存失败',
				  icon: 'none',
				  duration: 1000
				}); 
				this.results=[];//重置诊断结果
			}  
		   
		  },
		  fail: (err) => {
		    uni.showToast({
		      title: '结果保存失败',
		      icon: 'none',
		      duration: 1000
		    }); 
			this.results=[];//重置诊断结果
		  },
		  complete: () => {
		    console.log('请求完成');
		  }
		});
	},
	diagnosis(grade){
		if(grade[0] >= 11) this.physique.push("气虚质");
		if(grade[1] >= 11) this.physique.push("阳虚质");
		if(grade[2] >= 11) this.physique.push("阴虚质");
		if(grade[3] >= 9 && grade[3] <= 10) this.physique.push("痰湿质");
		if(grade[4] >= 9 && grade[4] <= 10) this.physique.push("湿热质");
		if(grade[5] >= 9 && grade[5] <= 10) this.physique.push("血瘀质");
		if(grade[6] <= 8 && grade[6] > 0) this.physique.push("气郁质");
		if(grade[7] <= 8 && grade[7] > 0) this.physique.push("特禀质");
		if(grade[8] >= 17) {
			let count = 0 ;
			let flag8 = true ;
			let flag10 = true ;
			for (var i = 0; i <= 7; i++) {
				if (grade[i]>=10) flag10=false;
				if (grade[i]>=8) flag8=false;
			}
			if (flag8) {
				this.physique.push("平和质")
			} else if(flag10){
				this.physique.push("基本是平和质")
			}
		};
		console.log(this.physique)
		uni.showModal({
				title: '诊断结果',
				content: this.physique.join(","),
				success: (res)=> {
				if (res.confirm) {
				    // 执行确认后的操作
					this.save();
				} 
				else {
					
					// 执行取消后的操作
					this.physique=[];//重置诊断结果
					uni.showToast({
					  title: '结果未保存',
					  icon: 'none',
					  duration: 1000
					}); 
				}
			}
		})
	},
		
  }
};
</script>

<style>
.container {
  padding: 20px; /* 容器内边距 */
  padding-bottom: 100px;
}

.input-container {
  display: flex;
  align-items: center; /* 垂直居中对齐 */
  margin-bottom: 10px; /* 根据需要调整间距 */
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

.question-container {
  margin-bottom: 20px; /* 问题间距 */
  background-color: #f9f9f9; /* 背景色 */
  border-radius: 8px; /* 边角圆滑 */
  padding: 15px; /* 问题内边距 */
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1); /* 盒阴影效果 */
}

.question-title {
  font-weight: bold; /* 标题加粗 */
  margin-bottom: 10px; /* 标题与选项间距 */
  color: #333; /* 标题颜色 */
}

.options-container {
  display: flex; /* 选项布局 */
  flex-direction: column; /* 选项竖直排列 */
}

.option {
  margin: 5px 0; /* 选项间距 */
  padding: 10px; /* 选项内边距 */
  border-radius: 5px; /* 选项边角圆滑 */
  transition: background-color 0.3s; /* 背景色过渡动画 */
}

.option:hover {
  background-color: #e6e6e6; /* 鼠标悬停时选项背景色 */
}

.save-button-container {
  display: flex; /* 按钮容器布局 */
  justify-content: center; /* 按钮居中对齐 */
  margin-top: 20px; /* 与问题间距 */
}

.save-button {
  padding: 10px 30px; /* 按钮内边距 */
  color: white; /* 按钮文字颜色 */
  background-color: #1a73e8; /* 按钮背景色 */
  border: none; /* 无边框 */
  border-radius: 4px; /* 按钮边角圆滑 */
  font-size: 16px; /* 文字大小 */
  cursor: pointer; /* 鼠标样式 */
}

.save-button:hover {
  background-color: #1669c7; /* 鼠标悬停时按钮背景色 */
}
</style>