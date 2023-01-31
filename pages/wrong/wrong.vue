<template>
	<view class="container">
		<view class="errors">
			<view class="errors-num" >
				<text class="title">{{total}}</text>
				<text class="info">错题数量</text>
			</view>
			<view class="button" @click="exam">
				<button type="default" class="goErrors">全部错题</button>
			</view>
			<view class="panel">
				<view class="panel-item">
					<view class="today">
						<text>今日错题</text>
					</view>
					<view class="errors-box"  @click="exam"> 
						<text class="num">共错{{total}}题</text>
						<uni-icons type="arrowright"></uni-icons>
					</view>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
userid: null,
total: 0
			};
		},
		onLoad() {
			this.userid = getApp().globalData.userid
		},
		onShow() {
			this.getErrors()
		},
		methods:{
			getErrors() {
				if (!getApp().globalData.userid) return uni.showToast({
					icon: 'error',
					title:'登录失效！'
				})
				uni.request({
					url:`${getApp().globalData.url}/topics/GetWrong`,
					data : {
						id: getApp().globalData.userid,
						page: 1,
						num: 10
					},
					success: ({data:res}) => {
						console.log(res)
						this.total = res.amount
					}
				})
			},
			exam() {
				if(this.total !==0) {
					console.log(this.total)
					uni.navigateTo({
						url: '../../sub1/errQuestion/errQuestion?num=3'
					})
				}else {
					return uni.showToast({
						title:'没有题目刷了',
						icon:'error'
					})
				}
			},
		}
	}
</script>

<style lang="scss">
	page {
		background-color: white;
	}

	.container {
		.errors {
			display: flex;
			flex-direction: column;
			align-items: center;

			.errors-num {
				display: flex;
				flex-direction: column;
				align-items: center;
				justify-content: center;
				margin: 20rpx 0;
				width: 260rpx;
				height: 260rpx;
				background: linear-gradient(to top, lightblue,rgb(189, 232, 229));
				border-radius: 50%;
				color: white;

				.title {
					font-size: 58rpx;
					font-weight: 700;
				}

				.info {
					font-size: 28rpx;
				}
			}

			.button {
				margin: 20rpx 0;
				width: 80%;

				.goErrors {
					border-radius: 20rpx;
					background: linear-gradient(to left, lightblue, rgb(189, 232, 229));
					color: white;
				}
			}
		}
	}

	.panel {
		border-top: 1px solid #EBEEF5;

		.panel-item {
			height: 130rpx;

			.today {
				font-weight: 800;
				font-size: 40rpx;
			}

			.errors-box {
				margin-right: 20rpx;
				.num {
					padding-right: 10rpx;
				}
			}
		}
	}
</style>
