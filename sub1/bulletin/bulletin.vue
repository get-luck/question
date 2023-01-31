<template>
	<view>
		<view class="bulletin-box">
			<view class="bulletin-item" v-for="(item,i) in activeList" :key='i'>
				<text class="title">{{item.title}}</text>
				<view class="content">
					{{item.content}}
				</view>
			</view>
		</view>
	</view>
	
</template>

<script>
	export default {
		data() {
			return {
				url: '99',
				activeList: [],
			}
		},
		onLoad() {
			const url = getApp().globalData.url
			this.url = url
			this.getNotive()

		},
		methods: {
			getNotive() {
				uni.request({
					method:'get',
					url: `${this.url}/Notice/GetNotices`,
					success: ({data: res}) => {
						this.activeList = res.data

					}
				})
			}
		}
	}
</script>

<style lang="scss">
	page {
		width: 100%;
		background-color: #efefef;
	}
	.bulletin-box {
		display: flex;
		justify-content: center;
		align-items: center;
		flex-direction: column;
		width: 100%;
		.bulletin-item {
			display: flex;
			flex-direction: column;
			width: 95%;
			height: 200rpx;
			background-color: white;
			margin: 6rpx 0;
			border-radius: 10px;
			.title {
				font-size: 34rpx;
				font-weight: bold;
				padding: 10rpx 20rpx 5rpx;
				overflow: hidden;
				white-space: nowrap;
				text-overflow: ellipsis;
				
			}
			.content {
				margin: 10rpx 0 0 ;
				padding: 5rpx 20rpx;
	  overflow: hidden;
	     text-overflow: ellipsis;
	     display: -webkit-box;
	     -webkit-box-orient: vertical;
	     -webkit-line-clamp:3; // 行数
			}
		}
	}

</style>
