<template>
  <view class="container">
    <view class="question-container" v-for="(question, index) in questions" :key="index">
      <view class="question-title">{{ index + 1 }}. {{ question.title }}</view>
      <radio-group class="options-container" v-model="question.answer" @change="saveResults($event, index)">
        <radio class="option" v-for="(option, optionIndex) in question.options" :key="optionIndex" :data-question-index="optionIndex" :value="option">{{ option }}</radio>
      </radio-group>
    </view>
    <view class="save-button-container">
      <button class="save-button" @click="submit">提交进行诊断</button>
    </view>	
  </view>
</template>
	
<script>
export default {
  data() {
    return {
      questions: [
        {
          title: '您精力充沛吗? (指精神头足，乐于做事)',
          options: ['没有', '很少', '有时', '经常', '总是'],
          answer: ''
        },
        {
          title: '您容易疲乏吗? (指体力如何，是否稍微活动一下或做一点家务劳动就感到累)',
          options: ['没有', '很少', '有时', '经常', '总是'],
          answer: ''
        },
		{
		  title: '您容易气短，呼吸短促，接不上气吗?',
		  options: ['没有', '很少', '有时', '经常', '总是'],
		  answer: ''
		},
		{
		  title: '您说话声音低弱无力吗?(指说话没有力气)',
		  options: ['没有', '很少', '有时', '经常', '总是'],
		  answer: ''
		},
		{
		  title: '您感到闷闷不乐、情绪低沉吗?(指心情不愉快，情绪低落)',
		  options: ['没有', '很少', '有时', '经常', '总是'],
		  answer: ''
		},
		{
		  title: '您容易精神紧张、焦虑不安吗?(指遇事是否心情紧张)',
		  options: ['没有', '很少', '有时', '经常', '总是'],
		  answer: ''
		},
		{
		  title: '您因为生活状态改变而感到孤独、失落吗?',
		  options: ['没有', '很少', '有时', '经常', '总是'],
		  answer: ''
		},
		{
		  title: '您容易感到害怕或受到惊吓吗?',
		  options: ['没有', '很少', '有时', '经常', '总是'],
		  answer: ''
		},
		{
		  title: '您感到身休超重不轻松吗?(感觉身休沉重)[BMI指数=休重 (kg) /身高2 (m)]',
		  options: ['没有', '很少', '有时', '经常', '总是'],
		  answer: ''
		},
		{
		  title: '您眼睛干涩吗?',
		  options: ['没有', '很少', '有时', '经常', '总是'],
		  answer: ''
		},
		{
		  title: '您手脚发凉吗?(不包含因周围温度低或穿的少导致的）',
		  options: ['没有', '很少', '有时', '经常', '总是'],
		  answer: ''
		},
		{
		  title: '您胃院部、背部或腰膝部怕冷吗? (指上腹部、背部、腰部或膝关节等，有一处或多处怕冷)',
		  options: ['没有', '很少', '有时', '经常', '总是'],
		  answer: ''
		},
		{
		  title: '您比一般人耐受不了寒冷吗? (指比别人容易害怕冬天或是夏天的冷空调、电扇等)',
		  options: ['没有', '很少', '有时', '经常', '总是'],
		  answer: ''
		},
		{
		  title: '您容易患感冒吗? (指每年感冒的次数)',
		  options: ['没有', '很少', '有时', '经常', '总是'],
		  answer: ''
		},
		{
		  title: '您没有感冒时也会息寒、流息涕吗?',
		  options: ['没有', '很少', '有时', '经常', '总是'],
		  answer: ''
		},
		{
		  title: '您有口粘口腻，或睡眠打鼾吗?',
		  options: ['没有', '很少', '有时', '经常', '总是'],
		  answer: ''
		},
		{
		  title: '您容易过敏(对药物、食物、气味、花粉或在季节交替、气候变化时)吗?',
		  options: ['没有', '很少', '有时', '经常', '总是'],
		  answer: ''
		},
		{
		  title: '您的皮肤容易起尊麻疹吗? (包括风团、风疹块、风挖瘩)',
		  options: ['没有', '很少', '有时', '经常', '总是'],
		  answer: ''
		},
		{
		  title: '您的皮肤在不知不觉中会出现青紫瘀斑、皮下出血吗?(指皮肤在没有外伤的情况下出现青一块紫一块的情况）',
		  options: ['没有', '很少', '有时', '经常', '总是'],
		  answer: ''
		},
		{
		  title: '您的皮肤一抓就红，并出现抓痕吗?(指被指甲或钝物划过后皮肤的反应)',
		  options: ['没有', '很少', '有时', '经常', '总是'],
		  answer: ''
		},
		{
		  title: '您皮肤或口唇干吗?',
		  options: ['没有', '很少', '有时', '经常', '总是'],
		  answer: ''
		},
		{
		  title: '您有肢体麻木或固定部位疼痛的感觉吗?',
		  options: ['没有', '很少', '有时', '经常', '总是'],
		  answer: ''
		},
		{
		  title: '您面部或鼻部有油腻感或者油亮发光吗?(指脸上或鼻子)',
		  options: ['没有', '很少', '有时', '经常', '总是'],
		  answer: ''
		},
		{
		  title: '您面色或目眶晦躇，或出现祸色斑块/斑点吗?',
		  options: ['没有', '很少', '有时', '经常', '总是'],
		  answer: ''
		},
		{
		  title: '您有皮肤湿疹、疮疗吗?',
		  options: ['没有', '很少', '有时', '经常', '总是'],
		  answer: ''
		},
		{
		  title: '您感到口干明燥、总想喝水吗?',
		  options: ['没有', '很少', '有时', '经常', '总是'],
		  answer: ''
		},
		{
		  title: '您感到口苦或嘴里有异味吗? (指口苦或口臭)',
		  options: ['没有', '很少', '有时', '经常', '总是'],
		  answer: ''
		},
		{
		  title: '您腹部肥大吗?(指腹部脂肪肥厚)',
		  options: ['没有', '很少', '有时', '经常', '总是'],
		  answer: ''
		},
		{
		  title: '您吃(喝)凉的东西会感到不舒服或者怕吃(喝)凉的东西吗? (指不喜欢吃凉的食物，或吃了凉的食物后会不舒服)',
		  options: ['没有', '很少', '有时', '经常', '总是'],
		  answer: ''
		},
		{
		  title: '您有大便黏滞不爽、解不尽的感觉吗?(大便容易粘在马桶或便坑壁上)',
		  options: ['没有', '很少', '有时', '经常', '总是'],
		  answer: ''
		},
		{
		  title: '您容易大便干燥吗?',
		  options: ['没有', '很少', '有时', '经常', '总是'],
		  answer: ''
		},
		{
		  title: '您舌苔厚腻或有舌苔厚厚的感觉吗?(如果自我感觉不清楚可由调查员观察后填写)',
		  options: ['没有', '很少', '有时', '经常', '总是'],
		  answer: ''
		},
		{
		  title: '您舌下静脉瘀紫或增粗吗? (可由调查员辅助观察后填写)',
		  options: ['没有', '很少', '有时', '经常', '总是'],
		  answer: ''
		},
      ],
      results: [],
	  grade: [],
	  physique: [],
    };
  },
  
  

  methods: {	
    saveResults(event,questionIndex) {
	  let  i = this.questions[questionIndex].options.indexOf(event.detail.value);
      this.questions[questionIndex].answer=i+1;
    },
	
	submit() {
	     this.results = this.questions.map((question, index) => {
	          return {
	            title: question.title,
	            answer: question.answer,
	          };
	        });
		 let result = this.results;
		this.grade[0]=result[1].answer+result[2].answer+result[3].answer+result[13].answer;
		this.grade[1]=result[10].answer+result[11].answer+result[12].answer+result[28].answer;
		this.grade[2]=result[9].answer+result[20].answer+result[25].answer+result[30].answer;
		this.grade[3]=result[8].answer+result[15].answer+result[27].answer+result[31].answer;
		this.grade[4]=result[22].answer+result[24].answer+result[26].answer+result[29].answer;
		this.grade[5]=result[18].answer+result[21].answer+result[23].answer+result[32].answer;
		this.grade[6]=result[4].answer+result[5].answer+result[6].answer+result[7].answer;
		this.grade[7]=result[14].answer+result[16].answer+result[17].answer+result[19].answer;
		this.grade[8]=result[0].answer+this.reverse(result[1].answer)+this.reverse(result[3].answer)+this.reverse(result[4].answer)+this.reverse(result[12].answer);
	
		this.diagnosis(this.grade)
	},
	reverse(i){
		if(i===1) return 5
		if(i===2) return 4
		if(i===3) return 3;
		if(i===4) return 2;
		if(i===5) return 1;
		return i
	},
	save(){
		uni.request({
		  url: 'http://localhost:9002/wenzhen/save',
		  method: 'POST',
		  data:{
			  token:uni.getStorageSync('token'),
			  result:this.physique.join(","),
		  },
		  success: (res) => {
			if(res.statusCode >= 200 && res.statusCode < 300){
				uni.showToast({
				  title: '结果已保存',
				  icon: 'none',
				  duration: 1000
				}); 
				this.physique=[];//重置诊断结果
			}else{
				uni.showToast({
				  title: '结果保存失败',
				  icon: 'none',
				  duration: 1000
				}); 
				this.physique=[];//重置诊断结果
			}  
		   
		  },
		  fail: (err) => {
		    uni.showToast({
		      title: '结果保存失败',
		      icon: 'none',
		      duration: 1000
		    }); 
			this.physique=[];//重置诊断结果
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