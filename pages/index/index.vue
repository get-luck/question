<template>
	<view>
		<!-- 轮播 -->
		<swiper :indicator-dots="true" :autoplay="true" :interval="3000" :circular="true">
			<swiper-item v-for="(item,i) in swiperList" :key="i" class="swiper">
				<view class="swiper-item">
					<image :src="item.head" mode=""></image>
				</view>
			</swiper-item>

		</swiper>

		<my-bulletin></my-bulletin>
		<!-- 主界面 -->
		<view class="question-wrap">
			<view class="question">
				<view class="left" @click="exam">
					<view class="button">
						<image mode="aspectFit" src="../../static/images/sjlx.png"></image>
						<text>随机练习</text>
					</view>
				</view>
				<view class="center">
					<view class="button">
						<view class="progress" @click="exam">
							<view class="progress-wrap">
								<view class="progress-title">一次性练习</view>
								<text>{{afternum}}/{{questionnum}}</text>
							</view>
							<image mode="widthFix" src="../../static/images/green.png"></image>
						</view>
					</view>
				</view>
				<view class="right">
					<view class="button" @click="goErrors">
						<image mode="aspectFit" src="../../static/images/wdct.png"></image>
						<text>我的错题</text>
					</view>
				</view>
			</view>
		</view>
		<!-- 第二个分类 -->
		<view class="question-wrap">
			<view class="question">
				<view class="left">
					<view class="button" @click="goResult">
						<image mode="aspectFit" src="../../static/images/wdcj.png"></image>
						<text>我的成绩</text>
					</view>
				</view>
				<view class="center">
					<view class="button">
						<view class="progress  blue" @click="goExam">
							<view class="progress-wrap">
								<view class="progress-title">模拟考试</view>
								<text>最佳成绩</text>
								<text>{{maxf}}分</text>
							</view>
							<image mode="widthFix" src="../../static/images/blue.png"></image>
						</view>
					</view>
				</view>
				<view class="right">
					<view class="button" @click="goCollection">
						<image mode="aspectFit" src="../../static/images/wdsc.png"></image>
						<text>我的收藏</text>
					</view>
				</view>
			</view>
		</view>
		<!-- 分界线 -->
		<view class="panel">
			<view class="panel-item" @click="goClassify">
				<view class="panel-left">
					<image src="../../static/answer-select.png" mode=""></image>
					<text>选择题目分类</text>
				</view>
				<uni-icons type="arrowright"></uni-icons>
			</view>
		</view>
<view class="classify">
	当前分类是： {{classifyID}}
</view>
<!-- 		<button type="default" @click="handle()">加个分类</button> -->
	</view>
</template>

<script>
	export default {
		data() {
			return {
				maxf: 100,
				afternum: 0,
				url: '',
				questionnum: 1000,
				swiperList: [],
				classifyID: '',
				userid: false,
				sid: null,
			}
		},
		onLoad() {
			uni.showShareMenu({
			  withShareTicket: true,
			  menus: ['shareAppMessage', 'shareTimeline']
			})
			this.userid = getApp().globalData.userid || false
			this.url = getApp().globalData.url
			this.getSwiper()
		},
		onShow() {
		this.classifyID = uni.getStorageSync ('idName') || '你没有选择分类'
		this.userid = getApp().globalData.userid || false
		this.sid = uni.getStorageSync ('tid') || false
		},
		onPullDownRefresh() {
			this.getSwiper()
			setTimeout(() => {
				uni.stopPullDownRefresh()
			}, 1000)

		},
		methods: {
			exam() {
				if (!this.sid) return uni.navigateTo({
					url:'../../sub1/classify/classify'
				})
	if (!this.userid) {
		uni.showToast({
			title:'登录失效！！',
			icon:'error'
		})
		return this.getinfo()
	}
				this.addRecord()
				uni.navigateTo({
					url: '../../sub1/testQuestion/testQuestion?num=3&sid=' +this.sid
				})
			},
			goClassify() {
				uni.navigateTo({
					url: '../../sub1/classify/classify'
				})
			},
			goCollection() {
				uni.navigateTo({
					url: '../../sub1/collection/collection'
				})
			},
			goErrors() {
				if (!this.sid) return uni.navigateTo({
					url:'../../sub1/classify/classify'
				})
				if (!this.userid) {
					uni.showToast({
						title:'登录失效！！',
						icon:'error'
					})
					return this.getinfo()
				}
				uni.switchTab({
					url: '../wrong/wrong'
				})
			},
			goResult() {
				if (!this.sid) return uni.navigateTo({
					url:'../../sub1/classify/classify'
				})
				if (!this.userid) return uni.showToast({
					title:'登录失效！！',
					icon:'error'
				})
				this.addRecord()
				uni.navigateTo({
					url: '../../sub1/result/result?id=' +this.userid
				})
			},
			getSwiper() {
				uni.request({
					method: 'GET',
					url: `${getApp().globalData.url}/Rotation/GetRotations`,
					data: {
						limit: 10,
						page: 1
					},
					success: ({
						data: res
					}) => {
						//修改一下这个图片属性
						res.data = res.data.filter(v => v.head = this.url +'/' + v.head)

						this.swiperList = res.data
					}
				})
			},
			goExam() {
				if (!this.sid) return uni.navigateTo({
					url:'../../sub1/classify/classify'
				})
				if (!this.userid) {
					uni.showToast({
						title:'登录失效！！',
						icon:'error'
					})
					return this.getinfo()
				}
				this.addRecord()
				uni.navigateTo({
					url: `../../sub1/questions/questions?sid=${this.sid}&classify=临时分类`
				})
				
			},
			addRecord() {
				uni.request({
					method: 'post',
					url: `${this.url}/Learning/AddLearning`,
					data: {
						uid: this.userid,
						sid: this.sid
					
					},
					success: ({
						data: res
					}) => {
						console.log(res)
					}
				})
			},
			async getinfo() {
				var a = ''
				await wx.login({
				  success (res) {
				    console.log(res.code)
					a =res.code
				    } 
				})
			
				uni.getUserProfile({
					desc: '登录',
					success:(res)=> {
						this.avatarUrl = res.userInfo.avatarUrl
						this.name = res.userInfo.nickName
						uni.setStorageSync('nickName',res.userInfo.nickName)
						uni.setStorageSync('avatarUrl',res.userInfo.avatarUrl)
						this.sex = res.userInfo.gender
						this.sex = this.sex ===0 ? '男' : '女'
						this.token = true
						console.log(res)
							uni.request({
								method:'post',
								url: `${getApp().globalData.url}/user/LogUser`,
								data: {
									'name': this.name,
									'Sex': this.sex ,
									'Head': this.avatarUrl,
									'code': a
								},
								success: (res) => {
									console.log(res)
									this.token = res.data.token
								
									uni.setStorageSync('userid', res.data.id)
									this.userid = res.data.id
									getApp().globalData.userid = res.data.id
									//token
									uni.setStorageSync('token',JSON.stringify(res.data.token))
									
								},
								fail: (err) => {
									console.log(err)
								}
							})
						
					},
					fail: (res) => {
						uni.showToast({
							title: '你取消了' + res,
							icon: 'none'
						})
					}
				})
			
			},
			handle() {
				//这个测试post请求了！！
				uni.request({
					method: 'get',
					url: `${this.url}/Manage/LogOn`,
					data: {
						account: 212,
						password: 390

					},
					success: ({
						data: res
					}) => {
						console.log(res)
					}
				})
			}
		},
		 
	}

</script>

<style lang="scss">
	page {
		background: #f5f6f7;
	}

	.swiper {
		height: 350rpx;

		.swiper-item {
			width: 100%;
			height: 100%;

			image {
				width: 100%;
				height: 100%;
			}
		}

	}

	.question-wrap {
		background: #fff;

	}


	.question {
		height: 350rpx;
		width: 100%;
		display: flex;
		justify-content: space-around;
		align-items: center;

		.left,
		.right {
			width: 205rpx;

			image {
				width: 50rpx;
				height: 50rpx;
			}
		}

		.center {
			width: 220rpx;
			height: 220rpx;
			display: flex;
			justify-content: center;
			align-items: center;
			position: relative;

			.progress {
				width: 200rpx;
				height: 200rpx;
				display: flex;
				flex-direction: column;
				align-items: center;
				justify-content: center;
				border-radius: 50%;
				background: linear-gradient(to bottom right, #59b6fb, #40abfa);

				&.blue {
					background: linear-gradient(to bottom right, #1bd0ad, #1bd0ad);
				}

				.progress-wrap {
					line-height: 24px;
					color: white;

					text {
						display: block;
						text-align: center;
					}

					.progress-title {
						font-size: 33rpx
					}
				}
			}

			image {
				position: absolute;
				top: 0;
				left: 0;
				width: 100%;
				height: 100%;
			}
		}

		.button {
			display: flex;
			flex-direction: column;
			align-items: center;
		}
	}

	.panel {
		height: 80rpx;

		.panel-item {
			image {
				width: 40rpx;
				height: 40rpx;
			}

			.panel-left {
				display: flex;
				align-items: center;

				text {
					margin-left: 20rpx;
				}
			}
		}
	}
	.classify {
		margin:10px  ;
	}
</style>
