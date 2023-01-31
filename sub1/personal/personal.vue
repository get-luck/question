<template>
	<view>
		<view class="panel">
			<view class="panel-item" style="position: relative">
				<text>头像</text>
				<image :src="userInfo.head" mode="widthFix" class="img" ></image>
				<uni-icons type="compose" @click="openimage" style="position: absolute;right: -20px"></uni-icons>
			</view>
			<view class="panel-item" style="position: relative">
				<text>昵称</text><text>{{userInfo.name}}</text>
				<uni-icons type="compose" @click="open" style="position: absolute;right: -20px"></uni-icons>
			</view>
			<view class="panel-item">
				<text>性别</text><text>{{userInfo.sex}}</text>
			</view>
			<view class="panel-item">
				<text>注册时间</text><text>{{userInfo.date |date}}</text>
			</view>
		</view>

		<uni-popup ref="popup" type="dialog">
			<uni-popup-dialog mode="input" message="成功消息"  title="修改昵称"
			:duration="2000" :before-close="true" @close="close" :value="userInfo.name"
				@confirm="confirm"></uni-popup-dialog>
		</uni-popup>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				userid: 0,
				token: '',
				userInfo: [],
				tempfile: null
			};
		},
		filters: {
			date: (v) => {
				if (v === undefined) return 0
				return v.substr(0, 10)
			}
		},
		onLoad(options) {
			this.token = JSON.parse(uni.getStorageSync('token')) || ''
			console.log(this.token)
			console.log(options)
			this.userid = +options.id
			this.getInfo()
		},
		methods: {
			getInfo() {

				uni.request({
					method: 'get',
					url: `${getApp().globalData.url}/User/Getuser`,
					header: {
						Authorization: "Bearer " + this.token
					},
					data: {
						id: this.userid
					},
					success: (res) => {

						console.log(res)
						if (res.statusCode === 401) {
							uni.switchTab({
								url: '../../pages/my/my'
							})
							return uni.showToast({
								title: '登录状态已失效！！！退出重新登录',
								icon: 'error'
							})
						}
						this.userInfo = res.data.data
						
					}
				})
			},
			open() {
				this.$refs.popup.open('center')
			},
			close() {

				this.$refs.popup.close()
			},
			confirm(value) {
				// 输入框的值
				console.log(value)
				uni.request({
					url:getApp().globalData.url + `/user/putusername`,
					method: 'put',
					data: {
						id : this.userid,
						name: value
					},
					success: (res) => {
						console.log(res)
						this.userInfo.name = value
					}
				})
				this.$refs.popup.close()
			},
			openimage() {
				uni.chooseImage({
					count: 1,
					sizeType: 'compressed',
					sourceType:['album', 'camera'],
					success: (res) => {
						if (res.tempFilePaths.length >=1) {
							this.tempfile = res.tempFilePaths[0]	
						} 
						console.log(this.tempfile )
						this.uploadfile()
					},
					fail: (res) => {
						console.log(res)
					}
				})
			},
			uploadfile() {
				console.log(this.tempfile)
				uni.uploadFile({
					url: getApp().globalData.url +`/user/postuserhead`,
					filePath: this.tempfile,
					name: 'file',
					header:{
						"Content-Type": "multipart/form-data",
					},
					formData: {
						key: '434353'
					},
					success: (res) => {
						const objdata= JSON.parse(res.data)
						console.log(objdata)
					},
					fail: (err) => {
						console.log(err)
					}
				})
				console.log('end')
			}
		}
	}
</script>

<style lang="scss">
	.panel .panel-item {
		height: 120rpx;
		font-size: 34rpx;
		font-weight: 600;

		text, {
			margin-right: 60rpx;
		}

		.img {
			width: 100rpx;
			border-radius: 10rpx;
			margin-right: 65rpx;
		}
	}
</style>
