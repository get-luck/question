<template>
	<view class="container">
		<view class="scroll-box">
			<scroll-view scroll-y="true" class="left">
				<view class="item" v-for="(item,i) in classifys" :key="i" @click="handle(item.id)" >
					<view :class="[item.id === current ? 'active': '']" style="width: 150rpx;">
						{{item.name}}
					</view>
				</view>

			</scroll-view>
			<scroll-view scroll-y="true" class="right" :scroll-top="scrollTop">
				<view class="itemBox">
					<view class="item" v-for="(item2,i2) in subject" :key="i2" v-if="item2.cId === current" @click="saveClassify(item2.id,item2.name)">
					<view :class="['subjectBox',currentID === item2.id ? 'cureent' : '' ]" >
					<image src="../../static/answer-select.png" mode="widthFix" style="width: 80rpx"></image>
					<text>{{item2.name}}</text>
				</view>

					</view>
				</view>
			</scroll-view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				isActive: true,
				current: 1,
				scrollTop: 0,
				currentID: null,
				classifys: [],
				subject: [],
				sname: null
			}
		},
		onLoad() {
			this.url = getApp().globalData.url
			this.currentID = uni.getStorageSync('tid') || ''
			this.getClassify()
			this.getsubject()
			this.current = uni.getStorageSync('classify') || ''
		},
		methods: {
			handle(i) {
			uni.setStorageSync('classify',i)
				this.current = i
				this.scrollTop = this.scrollTop === 0 ? 1 : 0
			},
			getClassify() {
				uni.request({
					url: `${this.url}/Classify/GetClassifys`,
					method: 'get',
					data: {
						limit: 20
					},
					success: ({
						data: res
					}) => {
						this.classifys = res.data
						console.log(this.classifys)
					}
				})
			},
			getsubject() {
				uni.request({
					url: `${this.url}/Subjects/GetSubjectss`,
					method: 'get',
					success: ({
						data: res
					}) => {
						this.subject = res.data
						console.log(res)
					}
				})
			},
			saveClassify(id,name) {
				console.log(id)
				this.currentID = id
				uni.setStorageSync('tid',id)
				uni.setStorageSync('idName',this.classifys[this.current -1].name+ ' & ' +name)
			uni.navigateBack()
			}
		}
	}
</script>

<style lang="scss">
	page,
	.container {
		height: 100%;
	}

	.scroll-box {
		display: flex;
		font-size: 40rpx;
		height: 100%;

		.left {
			width: 300rpx;
			border-right: 1px solid #ddd;
			box-sizing: border-box;

			.active {
				color: red;
				position: relative;
				background-color: #EEEEEE;

				&::after {
					content: '';
					width: 3px;
					height: 60rpx;
					background-color: red;
					position: absolute;
					top: 50%;
					transform: translateY(-50%);
					left: 0;
				}
			}

			view {
				height: 90rpx;
				line-height: 90rpx;
				text-align: center;
			}
		}

		.right {
			.itemBox{
				display: flex;
				.item {
					// display: flex;
					width: 240rpx;
					margin: 10px;
				}
				.subjectBox {
					border-radius: 20rpx;
					border: 1px solid #0081FF;
					display: flex;
					flex: 2;
					height: 140rpx;
					width: 240rpx;
					flex-direction: column;
					align-items: center;
				}
				.cureent {
					background: rgba(170, 255, 255, 0.6);
				}
			}
			

		}
	}
</style>
