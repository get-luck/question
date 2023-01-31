<script>
	export default {
		globalData:{
			activeList: [],
			url: 'http://192.168.2.107:8077',
			userid: false
		},
		onLaunch: function() {
			console.warn('当前组件仅支持 uni_modules 目录结构 ，请升级 HBuilderX 到 3.1.0 版本以上！')
			console.log('App Launch')
				// userid: 
				this.globalData.userid = uni.getStorageSync('userid') || ''

		},
		onShow: function() {
			console.log('App Show')
		},
		onHide: function() {
			console.log('App Hide')
		}
	}
</script>

<style lang="scss">
	@import "colorui/main.css";
	@import "colorui/icon.css";
	/*每个页面公共css */
	.panel {
		background-color: white;
		width: 96%;
		margin: 20rpx auto;
		border-radius: 20rpx;
		.panel-item {
			height: 80rpx;
			display: flex;
			border-bottom: 1px solid #EBEEF5;
			justify-content: space-between;
			align-items: center;
			margin-left: 20rpx;
		}
	}
</style>
