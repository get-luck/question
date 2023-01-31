<template>
	<view>
		<view class="header">
			<view class="login" v-if="token">
				<image :src="avatarUrl" mode=""></image>
				<text>{{userInfo.name || name}}</text>
			</view>
			<view class="login-before" v-else>
				<button type="default" class="button" @click="getinfo" >一键登录</button>
			</view>
		</view>
	
	<!-- 公告 -->
<my-bulletin></my-bulletin>
	
	<!-- 主体 -->
	<view class="container">
		<view class="panel">
			<view class="panel-item" @click="goPersonal">
				<text>个人信息</text>
				<uni-icons type="arrowright"></uni-icons>
			</view>
			<view class="panel-item" @click="goResult">
				<text>学习记录</text>
				<uni-icons type="arrowright"></uni-icons>
			</view>
			<view class="panel-item" @click="goCollection">
				<text>我的收藏</text>
				<uni-icons type="arrowright"></uni-icons>
			</view>
			<view class="panel-item" @click="goAbout">
				<text>关于我们</text>
				<uni-icons type="arrowright"></uni-icons>
			</view>
			<view class="panel-item" @click="exitLogin">
				<text>退出登录</text>
				<uni-icons type="arrowright"></uni-icons>
			</view>
			
		</view>
	</view>

	</view>
</template>

<script>
	export default {
		data() {
			return {
				name: '',
				avatarUrl: '',
				sex: '',
				token: false,
				userid: null,
				userInfo:[]
			};
		},
		onShow() {
			this.token = JSON.parse(uni.getStorageSync('token')) || ''
			this.name = uni.getStorageSync('nickName') || ''
			this.userid = uni.getStorageSync('userid') || ''
			this.avatarUrl = uni.getStorageSync('avatarUrl') || ''
			this.getInfo(this.userid)
						console.log(this.token)
		},
		methods: {
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
			getInfo(id) {
			
				uni.request({
					method: 'get',
					url: `${getApp().globalData.url}/User/Getuser`,
					header: {
						Authorization: "Bearer " + this.token
					},
					data: {
						id
					},
					success: (res) => {
			
						console.log(res)
						if (res.statusCode === 401) {
							uni.switchTab({
								url: '../../pages/my/my'
							})
							this.token = false
							return uni.showToast({
								title: '登录状态已失效！！！退出重新登录',
								icon: 'error'
							})
						}
						this.userInfo = res.data.data
						uni.setStorageSync('nickName',this.userInfo.name)
						uni.setStorageSync('avatarUrl',this.userInfo.head)
	
					}
				})
			},
			exitLogin() {
				if (!this.token) return uni.showToast({
					title:'没有登录哦',
					icon: 'none'
				})
				uni.showLoading({
					title:'加载中...'
				})
				setTimeout(()=> {
					this.token = '';
					uni.clearStorageSync()
						getApp().globalData.userid = false
					uni.hideLoading()
				},1000)
			},
			goAbout() {
				uni.navigateTo({
					url:"../../sub1/about/about"
				})
			},
			goResult() {
				uni.navigateTo({
					url:'../../sub1/result/result?id='+this.userid
				})
			},
			goPersonal() {
				uni.navigateTo({
					url:'../../sub1/personal/personal?id=' + this.userid
				})
			},
			goCollection() {
				uni.navigateTo({
					url:'../../sub1/collection/collection'
				})
			}
		}
	}
</script>

<style lang="scss">

	.header {
		width: 100%;
		height: 260rpx;
		background-color: #bab8b8;
		display: flex;
		flex-direction: column;
		justify-content: center;

		.login {
			display: flex;
			flex-direction: column;
			align-items: center;
			justify-content: center;

			image {
				width: 160rpx;
				height: 160rpx;
				border-radius: 50%;
			}

			text {
				font-size: 36rpx;
				color: white;
				padding-top: 10rpx;
			}
		}

		.login-before {
			width: 100%;

			.button {
				width: 90%;
				color: white;
				background: linear-gradient(to right top, #43c9d0, #1bd0ad);
				border-radius: 100px;
				margin: 0 auto;
				box-shadow: 0 1px 2px rgba(0, 0, 0, .6);
			}
		}

	}
	.container {
		.panel {
			background-color: white;
			width: 96%;
			margin: 20rpx auto;
			border-radius: 20rpx;
			.panel-item {
				height: 100rpx;
				display: flex;
				font-size: 34rpx;
				justify-content: space-between;
				align-items: center;
				margin-left: 20rpx;
			}
		}
	}
	


</style>
