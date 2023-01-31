<template>
	<view class="container">
		<view class="errors" v-if="!show">
			<view class="errors-num">
				<text class="title">{{topics.amount}}</text>
				<text class="info">收藏数量</text>
			</view>
			<view class="button">
				<button type="default" class="goErrors" @click="goShow">全部收藏</button>
			</view>
		</view>
		<showCollection :topics="topics.data" v-show="show" @updata ="updata"></showCollection>
	</view>
</template>

<script>
	export default {
		data() {
			return {
			topics: [],
			show: false
			};
		},
		onLoad() {
			this.getCollect()
		},
		methods:{
			getCollect() {
				uni.request({
					method: 'get',
					data: {
						id: getApp().globalData.userid
					},
					url: `${getApp().globalData.url}/collect/GetUser`,
					success: (res) => {
						this.topics = res.data
					}
				})
			},
			goShow() {
		this.show = !this.show
			},
			updata(v) {
				this.getCollect()
			}
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
