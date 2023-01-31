<template>
	<view>
		<view class="panel" v-for="(item,i) in topics" :key="i">
			<view class="title">
				<text class="biaoti">标题:</text>
			{{item.title}}
			</view>
			<view class="answer">
				<text class="biaoti">答案: </text>
				{{item.answer}}
				
			</view>
			<button type="primary" size="mini" style="float: right; " @click="delCollection(item.id)">取消收藏</button>
		</view>
	</view>
</template>

<script>
	export default {
		name:"showCollection",
		data() {
			return {
				newtopics: []
			};
		},
		props: ['topics'],
		mountd() {
			if (this.topics) {
				this.newtopics = this.topics
			}
		},
		emits: ['updata'],
		methods: {
			delCollection(id) {
				this.addCollect(id)
			},
			addCollect(tid) {
				uni.request({
					method: 'post',
					data: {
						uid: getApp().globalData.userid,
						tid
					},
					url: `${getApp().globalData.url}/collect/AddCollect`,
					success: (res) => {
						this.$emit('updata',res)
					}
				})
			}
		}
	}
</script>

<style>
page {
	background-color: #eeeeee;
}
.panel {
	width: 90%;
	height: 200rpx;
	border-radius: 10rpx;
	background-color: white;

}
.title,.content {
	padding: 5px 0;
}
	.biaoti {
		font-size: 16px;
		font-weight: 600;
		
	}
</style>
