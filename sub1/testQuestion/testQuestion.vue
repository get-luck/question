<template>
	<view>

		<form id="frm" @submit="formSubmit">
			<view id="top-box" class="cu-bar bg-white solid-bottom">
				<!--题型-->
				<view class="action text-black">
					<text v-if="currentType===1">判断题</text>
					<text v-else-if="currentType===2">单选题</text>
					<text v-else-if="currentType===3">多选题</text>
				</view>
				<!--倒计时-->
				<!-- <view id="time" v-if="hiddeBtnAndTime">
					<uni-countdown :show-day="false" border-color="#ffffff" :hour="hour" :minute="minute" :second="second" @timeup="timeup"></uni-countdown>
				</view> -->
				<!--提交按钮-->
<!-- 				<view class="action" v-if="hiddeBtnAndTime">
					<button size="mini" form-type="submit" type="primary" @click="show()">提交</button>
					
					<unik-modal :class="[mask == true ? 'showMask':'']" ref="unikModal" :modalTitle="modalTitle" @confirmModal="confirmModal" @cancelModal="cancelModal">
						<view style='font-size: 14px;padding: 15px;'>
							还有 <text style="color: #f00;">{{ totalQuestionNumber + 1 }}</text> 题未做,
							是否确认提交?
						</view>
					</unik-modal>
				</view> -->
			</view>
			<!-- 题目 -->
			<swiper :current="questionIndex" class="swiper-box" @change="swiperChange" :style="{'height':swiperHeight}">
				<swiper-item v-for="(question,i) in questionList" :key="i">
					<!-- 题目 -->
					<view class="cu-bar bg-white solid-bottom">
						<view class="action text-black">
					
							<text class="cuIcon-title text-red"></text>{{i + 1}}. <text>{{question.title}}</text>
						</view>
						
					</view>
							<image v-if="question.head" :src="url +question.head" mode="widthFix" style="width: 400rpx;" @click="preImg(url +question.head)"></image>
					<view>
						<!--单选-->
						<radio-group class="block" @change="radioboxChange" v-if="question.type===2">
							<view class="cu-form-group" v-for="(option,k,index) in question.topics" :key="index">
								<radio :value="option" :disabled="question.activeDisabled"></radio>
								<view class="title text-black">{{k}}. 
								<text > {{option}}</text></view>
							</view>
						</radio-group>
						<!--判断-->
						<radio-group style="display: flex;justify-content: space-between; margin:0 30rpx" @change="radioboxChange" v-if="question.type===1">
							<view class="cu-form-group" v-for="(option,k,index) in question.topics" :key="index">
								<radio :value="option" :disabled="question.activeDisabled"></radio>
								<view class="title text-black">{{k}}. {{option}}</view>
							</view>
						</radio-group>
						<!--多选-->
						<checkbox-group class="block" @change="checkboxChange" v-else-if="question.type===3">
							<view class="cu-form-group" v-for="(option,k,index) in question.topics" :key="index">
								<checkbox :disabled="question.activeDisabled" :value="option" :class="question.userAnswer.indexOf(option) > -1?'checked':''"
								 :checked="question.userAnswer.indexOf(option) > -1?true:false"></checkbox>
								<view class="title text-black">
									<view>{{k}}.{{option}}</view>
								</view>
							</view>
								<button type="primary" @click="tijiao">（选择完成后）提交</button>
						</checkbox-group>
						<!-- 解析 -->
						<view  v-show="question.showAnswer" class="margin-top solid-top">
							<view class="cu-bar">
								<view class="action  text-grey">
									<text>正确答案：</text>
									<text class="solid-bottom  padding-left" :class="[question.state ? 'text-green' : '',!question.state && question.userAnswer === question.answer ? 'text-green':'text-red']">{{question.answer}}</text>
								</view>
							</view>
							<view class="cu-bar cu-bar-title">
								<view class="action  text-grey">
									<text>解析：</text>
								</view>
							</view>
							<view class="text-content padding  text-grey">
								{{subject.explain ? subject.explain : '这个题目没有解析，因没有!'}}
							</view>
						</view>
						<!-- 解析 -->
	
					</view>
					
				</swiper-item>
			</swiper>
		</form>
		<!-- 底部按钮 上一页 下一页 题卡 -->
		<view id="foot-box" class="cu-bar tabbar bg-white shadow foot">
			<view class="action" @click="moveQuestion(-1)">
				<view class="cuIcon-cu-image">
					<text class="lg cuIcon-back text-gray"></text>
				</view>
				<view class="text-gray">上一题</view>
			</view>
			<view class="action" @click="moveQuestion(1)">
				<view class="cuIcon-cu-image">
					<text class="lg text-gray cuIcon-right"></text>
				</view>
				<view class="text-gray">下一题</view>
			</view>
			<!-- 解析-->
			<view v-if="showAnswerBtn" class="action" @click="showAnswerChange">
				<view class="cuIcon-cu-image">
					<text class="lg text-gray cuIcon-question"></text>
				</view>
				<view class="text-gray">解析</view>
			</view>
		<!-- 收藏 -->
		<view class="action" @click="FavorSubject" >
			<view class="cuIcon-cu-image">
				<text class="lg cuIcon-favor" :class="[questionList[questionIndex].collect?'text-red':'text-gray']"></text>
			</view>
			<view  :class="[questionList[questionIndex].collect?'text-red':'text-gray']">收藏</view>
		</view>
			<!-- 提卡 -->
			<view class="action">
				<button class="cu-btn bg-green shadow" @tap="showCardModal" data-target="modalCard">题卡</button>
			</view>
			<view class="cu-modal" :class="modalCard=='modalCard'?'show':''" @tap="hideCardModal">
				<view class="cu-dialog" @tap.stop>

					<scroll-view class="page padding" :scroll-y=true :style="{'height':swiperHeight}">
						<view class="cu-bar solid-bottom">
							<view class="action">
								题卡
							</view>
						</view>
						<!-- 答题时 显示的题卡 -->
		<!-- 				<view class="grid col-5" v-if="activeShow">
							<view class="margin-tb-sm text-center" v-for="(question,index) in questionList" :key="index">
								<button class="cu-btn round" :class="[question.userAnswer.length===0?'line-grey':'bg-haveFinashed']" @click="appointedQuestion(index)">{{index+1}}</button>
							</view>
						</view> -->
						<!-- 提交之后显示的题卡 -->   
						<view class="grid col-5" >
							<view class="margin-tb-sm text-center" v-for="(question,index) in questionList" :key="index">
								<button class="cu-btn round" :class="[question.state ? 'line-grey' : '',!question.state && question.userAnswer === question.answer ? 'bg-haveFinashed':'bg-red']"
								 @click="appointedQuestion(index)">{{index+1}} </button>
							</view>
						</view>
					</scroll-view>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				mask:false,
				userFavor: false,//收藏
				hour:0,
				minute:30,
				second:0,
				answerArr: [], //定义一个数组 装答案
				userAnswerArr: [], //选择的答案的数组
				activeShow: true,
				confrimShow: false,
				confrimShows: [],
				showNameAndTel: false,
				hiddeBtnAndTime: true, //隐藏时间和提交按钮
				activeDisabled: false, //单选、多选按钮不可编辑
				showAnswerBtn: false, //显示答案解析
				name: "",
				phoneNumber: "",
				totalNumber: 0,
				modalTitle: '', //确认按钮标题
				currentType: 0, //当前题型
				totalQuestionNumber: 0, //总题目数
				questionIndex: 0, //跳转索引
				autoRadioNext: true, //单选题，自动移下一题
				swiperHeight: '800px', //
				modalCard: null, //显示答题卡
				rightQuestionNumber: 0, //正确题目数量
				rightNumber:0,
				mistakeQuestionNumber: 0, //错误题目数量
				mistakeNumber:0,
				userid: null,
				sid: null,
				url: '',
				questionList: [			], //网络题目获取地方
			}
		},

		onReady() {
			this.url = getApp().globalData.url +'/'
			var tempHeight = 800;
			var _me = this;
			uni.getSystemInfo({
				//获取手机屏幕高度信息，让swiper的高度和手机屏幕一样高                
				success: function(res) {
					tempHeight = res.windowHeight;
					uni.createSelectorQuery().select("#top-box").fields({
						size: true,
						scrollOffset: true
					}, (data) => {
						tempHeight -= data.height;
						uni.createSelectorQuery().select("#foot-box").fields({
							size: true,
							scrollOffset: true
						}, (data) => {
							tempHeight -= data.height;
							_me.swiperHeight = tempHeight + 'px';
						}).exec();

					}).exec();
				}
			});

		},
		onLoad(e) {
			this.num = e.num || 1
			this.sid = e.sid || 1
			this.userid = getApp().globalData.userid
			this.gettopics()
			
		},
		methods: {
			//显示题卡
			showCardModal: function(e) {
				this.modalCard = e.currentTarget.dataset.target
			},
			//隐藏题卡
			hideCardModal: function(e) {
				this.modalCard = null
			},
			//滑动事件
			swiperChange: function(e) {
				let index = e.target.current;
				if (index != undefined) {
					this.questionIndex = index;
					this.currentType = this.questionList[index].type;
				}
				console.log('获取滑动')
			},
			//单选选中
			radioboxChange: function(e) {
			
				var items = this.questionList[this.questionIndex].optionList;
				var values = e.detail.value; //e.detail.value 用户的选择
				console.log(this.questionList[this.questionIndex].showAnswer)
				//即可展示吧
				this.questionList[this.questionIndex].showAnswer = true
				this.questionList[this.questionIndex].activeDisabled = true
				this.questionList[this.questionIndex].state = false
				//再见了
					console.log(values)
				this.questionList[this.questionIndex].userAnswer = values;
				var flag = true;
				for (var i = 0; i< this.userAnswerArr.length; ++i ) {
					if (this.userAnswerArr[i].tid == this.questionList[this.questionIndex].id) {
						this.userAnswerArr[i].Choice = values;
						flag = false;
						break;
					}
				}
				if (flag) {
					this.userAnswerArr.push({
						tid: this.questionList[this.questionIndex].id,
						Choice: values
					});
				}
				this.questionList[this.questionIndex].showAnswer = true;
				uni.request({
					url:`${getApp().globalData.url}/History/AddHistory`,
					method:'post',
					data: {
						Uid: this.userid,
						data: this.userAnswerArr
					},
					success: (res) => {
						console.log(res.data.correct)
						if (!res.data.correct) return false
						if (this.autoRadioNext && this.questionIndex < this.questionList.length - 1) {
							// this.questionIndex += 1;
						};
					}
				})
				
				
				
			
				
			},
			//复选选中
			checkboxChange: function(e) {
				var items = this.questionList[this.questionIndex].topics;
				var values = e.detail.value;
				this.questionList[this.questionIndex].userAnswer = values;
				var lenI = items.length;
				var lenJ = values.length;
				console.log(items)
				console.log(values)
				var flag = true;
				for (var i = 0; i < this.userAnswerArr.length; ++i) {
					// console.log(this.questionList[this.questionIndex].id,this.userAnswerArr[i].tid)
					if (this.userAnswerArr[i].tid == this.questionList[this.questionIndex].id) {
						this.userAnswerArr[i].Choice = values.sort().join('');
						flag = false;
						break;
					}
				}
				if(flag) {
					this.userAnswerArr.push({
						tid: this.questionList[this.questionIndex].id,
						Choice: values.sort().join(',')
					});
				}
				
				uni.request({
					url:`${getApp().globalData.url}/History/AddHistory`,
					method:'post',
					data: {
						Uid: this.userid,
						data: this.userAnswerArr
					},
					success: (res) => {
						console.log(res)
					}
				})
			},
			
			tijiao() {
				//即可展示吧
				this.questionList[this.questionIndex].showAnswer = true
				this.questionList[this.questionIndex].activeDisabled = true
				this.questionList[this.questionIndex].state = false
				//再见了
			},
			//上一题、下一题
			moveQuestion: function(e) {
				if (e === -1 && this.questionIndex != 0) {
					this.questionIndex -= 1;
				}
				if (e === 1 && this.questionIndex < this.questionList.length - 1) {
					this.questionIndex += 1;
				}
			},
			//题卡指定
			appointedQuestion: function(e) {
				this.modalCard = null;
				this.questionIndex = e;
			},
			FavorSubject: function(e) { //收藏题
			this.addCollect()
			console.log(this.questionList[this.questionIndex])
				if(this.userFavor)
				{
					this.userFavor=false;
					
					this.questionList[this.questionIndex].collect=false;					
				}
				else{
					
					this.userFavor=true;
					this.questionList[this.questionIndex].collect=true;	
				}				
			},
			// 提交按钮
			formSubmit: function() {
				this.mask = !this.mask;
				for (var i = 0 ; i < this.answerArr.length ; i++) {
					for (var j = 0 ; j < this.userAnswerArr.length ;j++) {
						var answerStr = this.answerArr[i].answer.split(',').sort().toString();
						var userAnswerStr = this.userAnswerArr[j].Choice;
						if(this.answerArr[i].id === this.userAnswerArr[j].tid && answerStr === userAnswerStr) {
							this.rightQuestionNumber += 1;
							this.totalQuestionNumber -= 1;
						}else if (this.answerArr[i].id === this.userAnswerArr[j].tid && answerStr != userAnswerStr) {
							this.mistakeQuestionNumber += 1;
							this.totalQuestionNumber -= 1;
						}
					}
				}
			},
			show: function() {
				this.$refs.unikModal.show();
			},
			//确认提交
			confirmModal:function() {
				console.log(this.userid,this.userAnswerArr)
				uni.request({
					url:`${getApp().globalData.url}/History/AddHistory`,
					method:'post',
					data: {
						Uid: this.userid,
						data: this.userAnswerArr
					},
					success: (res) => {
						console.log(res)
					}
				})
				//var userAnswerMap = JSON.stringify(this.userAnswerMap);
				// uni.setStorageSync("userAnswerArr",this.userAnswerArr);
				// uni.redirectTo({
				// 	url : '../showAnswer/showAnswer',//?userAnswerMap=' + userAnswerMap,
				// 	success: res => {},
				// 	fail: () => {},
				// 	complete: () => {}
				// });
				this.rightNumber = this.rightQuestionNumber;
				this.mistakeNumber = this.mistakeQuestionNumber;
				var accuracy = Math.round((this.rightNumber / (this.totalNumber + 1) * 100) * 100) / 100;
			//这里停止
				this.showAnswerBtn = true;
				this.activeDisabled = true;
				this.hiddeBtnAndTime = false;
				this.showNameAndTel = true;
				this.activeShow = false;
				this.confrimShow = true;
				//这里停止
				uni.showModal({
					title: "答题情况",
					content: "共" + (this.totalNumber + 1) + "题," + "正确率:" + accuracy + "%" + ",正确:" + this.rightNumber +
						"题," + "错误:" + this.mistakeNumber + "题",
					showCancel: false,
					confirmText: "确定",
				});
			},
			//取消
			cancelModal() {
				this.mask = !this.mask;
				this.rightQuestionNumber = this.rightNumber;
				this.mistakeQuestionNumber = this.mistakeNumber;
				this.totalQuestionNumber = this.totalNumber;
			},
			//显示解析
			showAnswerChange: function(e) {
				if (this.questionList[this.questionIndex].showAnswer) {
					this.questionList[this.questionIndex].showAnswer = false;
				} else {
					this.questionList[this.questionIndex].showAnswer = true;
				}
			},
			//收藏
			addCollect() {
				uni.request({
					method: 'post',
					data: {
						uid: getApp().globalData.userid,
						tid: this.questionList[this.questionIndex].id
					},
					url: `${getApp().globalData.url}/collect/AddCollect`,
					success: (res) => {
						console.log(res)
					}
				})
			},
			preImg(v) {
				console.log(v)
				let img = [v]
				console.log(img)
			uni.previewImage({
				urls: img
			})
			},
			//倒计时结束之后自动提交
			// timeup: function() {
			// 	this.confirmModal();
			// },
			//获取题目 网络
			gettopics() {
					uni.request({
						method:'get',
						url: `${getApp().globalData.url}/Topics/SetTopics`,
						data: {
							id: getApp().globalData.userid,
							num: 3,
							sid: this.sid
						},
						success: ({data: res}) => {
							console.log(res)
							if( res.status === 201){ 
								uni.switchTab({
									url:'../../pages/index/index'
								})
								return uni.showToast({
								title:'题目已经刷完了！',
								icon:'error'
							})
							}
							//修复后端的代码
							// res.data.forEach(i=> {
							// 	 i.userAnswer = ''
							// })
							let arr = []
							res.data.forEach((i,k)=> {
								if (i !== null) {
									// console.log(k,i)
									arr.push(i)
									// res.data.splice(k,1)
								}
							})
						// console.log(arr)
							
							this.questionList = arr //替换修改的
							this.currentType = this.questionList[0].type;
							uni.hideLoading();
							for (var j = 0; j < this.questionList.length; j++) {
								this.totalQuestionNumber = j;
							}
							for (var j = 0; j < this.questionList.length; j++) {
								this.totalNumber = j;
							}
							//添加用户显示答案字段
							for (var i = 0; i < this.questionList.length; i++) {
								this.$set(this.questionList[i], "showAnswer", false);
								this.$set(this.questionList[i], "activeDisabled", false);
							}
							for (var i = 0; i < this.questionList.length; i++) {
								this.answerArr.push({id:this.questionList[i].id,answer:this.questionList[i].answer});
							}
							const name = uni.getStorageSync('name');
							const tel = uni.getStorageSync('tel');
							if (name) {
								this.name = name;
							}
							if (tel) {
								this.phoneNumber = tel;
							}
						}
					})
			}
		}
	}
</script>

<style>
	@import "../../colorui/animation.css";

	page {
		background-color: #FFFFFF;
	}

	.cu-form-group {
		justify-content: flex-start;
	}

	.cu-form-group .title {
		padding-left: 30upx;
		padding-right: 0upx;
	}

	.cu-form-group+.cu-form-group {
		border-top: none;
	}

	.cu-bar-title {
		min-height: 50upx;
	}

	.cu-list.menu>.cu-item-error {
		justify-content: flex-start;
	}

	.bg-haveFinashed {
		background-color: #4CD964;
		color: #ffffff;
	}

	.uni-padding-wrap {
		width: 690upx;
		padding: 0 30upx;
	}

	.uni-common-mt {
		margin-top: 30upx;
	}

	.uni-flex {
		display: flex;
		flex-direction: row;
	}

	.uni-row {
		flex-direction: row;
	}

	.text {
		margin: 15upx 10upx;
		padding: 0 20upx;
		/* background-color: #ebebeb; */
		height: 70upx;
		line-height: 70upx;
		text-align: center;
		color: #777;
		font-size: 26upx;
	}
	
	.showMask {  
	  position: fixed;  
	  top:0;  
	  left:0;  
	  z-index:999;  
	  width:100%;  
	  height:100vh;  
	  background:rgba(0,0,0,0.4);
	}  
</style>
