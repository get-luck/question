<template>
	<view class="container">
		<view class="about-title">
			<text class="title">刷题系统</text>
			<text>VerSion 2.0.1</text>
		</view>
		<view class="panel">
			<view class="panel-item">
				版本介绍：
					修复已经知道的问题！
			</view>
			<view class="panel-item">
					增加一些奇怪的bug，并增加错题本，等一系列功能。
			</view>		
				<view class="panel-item">
					实现了具体的答题功能，以及修复一些奇怪的bug。
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				
			};
		}
	}
</script>

<style lang="scss">
.container {

	.about-title {
		display: flex;
		flex-direction: column;
		align-items: center;
		font-size: 38rpx;
		padding: 10rpx 0;
		.title {
			font-weight: bold;
		}
	}
	.block {
		display: block;
	}
}
</style>
