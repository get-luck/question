<template>
	<view>
		<!-- 公告 -->
		<view class="info" @click="handle">
		    <view class="infor-box">
		        <view class="info-title">
		            <text>通知公告</text>
		        </view>
		        <navigator class="bulletin" url="/pages/_index/gonggao/gonggao" open-type="navigate">
		            <swiper autoplay="true" circular="true" class="swiper_container" interval="5000" vertical="true" >
		                <swiper-item v-for="(item,i) in activeList" :key="i">
		                    <view class="swiper_item"> {{item.title}}</view>
		                </swiper-item>
		            </swiper>
		        </navigator>
		    </view>
		</view>
	</view>
</template>

<script>
	export default {
		name:"my-bulletin",
		data() {
			return {
				url: '666',
				activeList: []
			};
		},
		mounted() {
			const url = getApp().globalData.url
			this.url = url
			this.getNotive()
		},
		methods:{
			handle() {
				uni.navigateTo({
					url:'../../sub1/bulletin/bulletin'
				})
			},
			getNotive() {
				uni.request({
					method:'get',
					url: `${this.url}/Notice/GetNotices`,
					success: ({data: res}) => {
						this.activeList = res.data
						console.log(this.activeList)
					}
				})
			}
		}
	}
</script>

<style lang="scss">

	.info {
		  width: 100%;
		  .infor-box {
		    width: 706rpx;
		    height: 90rpx;
		    background-color: #fff;
		    margin: 10rpx auto;
		    border-radius: 8rpx;
		    box-shadow: 0 3rpx 5rpx 1rpx #ccc;
			display: flex;
			align-items: center;
			justify-content: space-between;
		  }
		  .info-title {
		    width: 139rpx;
		    height: 42rpx;
		    background-color: #fe9602;
		    border-radius: 50rpx;
		    font-size: 23rpx;
		    color: #fff;
			margin-left: 30rpx;
		    text-align: center;
			line-height: 42rpx;
		  }
		  
		  .bulletin{
		  	  height: 100%;
		  	  width: 100%;
		  	padding-left: 30rpx;
		  	.swiper_container {
		  		height: 90rpx;
		  		overflow: hidden;
		  		.swiper_item {
		  		  height: 90rpx;
		  		  font-size: 24rpx;
		  		  line-height: 88rpx;
		  		  white-space: nowrap;
		  		  overflow: hidden;
		  		  text-overflow: ellipsis;
		  		}
		  	}
		}
		
	
		
	}
</style>
