<template>
	<view class="page">
		<view class="publish-top">
			<text class="cancel pressable" @click="cancel">取消</text>
			<text class="title">发布动态</text>
			<button class="publish-btn pressable" :class="{ active: canPublish }" @click="publish">{{ loading ? '发布中' : '发布' }}</button>
		</view>

		<textarea v-model="content" class="textarea" placeholder="分享你的现场、应援或和爱豆合拍人生四宫格" maxlength="300" />

		<view class="upload-grid">
			<view v-for="item in images" :key="item" class="upload-item">{{ item }}</view>
			<view class="upload-item add pressable" @click="addImage">＋</view>
		</view>

		<view class="select-card card pressable" @click="goSelectArtist">
			<text>关联艺人</text>
			<text class="muted">{{ artist || '选择艺人' }} ›</text>
		</view>

		<view class="section-head"><text class="section-title">推荐话题</text></view>
		<view class="topic-row">
			<text v-for="topic in topics" :key="topic" class="topic pressable" @click="content += ' ' + topic">{{ topic }}</text>
		</view>

		<AppTabbar current="community" />
	</view>
</template>

<script>
	import AppTabbar from '@/components/app-tabbar/app-tabbar.vue'

	export default {
		components: { AppTabbar },
		data() {
			return {
				content: '',
				artist: '',
				images: [1, 2],
				loading: false,
				topics: ['#四宫格合拍', '#人生照片', '#现场记录', '#应援日记']
			}
		},
		computed: {
			canPublish() {
				return this.content.trim().length > 0
			}
		},
		methods: {
			cancel() {
				uni.navigateBack()
			},
			addImage() {
				this.images.push(this.images.length + 1)
			},
			goSelectArtist() {
				uni.navigateTo({ url: '/pages/search/search?mode=frame' })
			},
			publish() {
				if (!this.canPublish) return
				this.loading = true
				setTimeout(() => {
					this.loading = false
					uni.reLaunch({ url: '/pages/community/community' })
				}, 500)
			}
		}
	}
</script>

<style lang="scss" scoped>
	.publish-top {
		display: grid;
		grid-template-columns: 90rpx 1fr 100rpx;
		align-items: center;
		margin-bottom: 24rpx;
	}

	.cancel {
		color: #6B7280;
		font-size: 28rpx;
	}

	.title {
		text-align: center;
		font-size: 34rpx;
		font-weight: 900;
	}

	.publish-btn {
		height: 58rpx;
		border-radius: 8px;
		background: #D1D5DB;
		color: #FFFFFF;
		font-size: 24rpx;
	}

	.publish-btn.active {
		background: #7C3AED;
	}

	.textarea {
		width: 100%;
		min-height: 260rpx;
		padding: 22rpx;
		border-radius: 8px;
		background: #FFFFFF;
		font-size: 30rpx;
		line-height: 1.55;
	}

	.upload-grid {
		display: grid;
		grid-template-columns: repeat(3, 1fr);
		gap: 14rpx;
		margin-top: 18rpx;
	}

	.upload-item {
		height: 210rpx;
		border-radius: 8px;
		background: linear-gradient(135deg, #F3E8FF, #E0F2FE);
		display: flex;
		align-items: center;
		justify-content: center;
		color: #7C3AED;
		font-weight: 900;
	}

	.upload-item.add {
		background: #FFFFFF;
		border: 2rpx dashed #D1D5DB;
		font-size: 42rpx;
	}

	.select-card {
		height: 96rpx;
		margin-top: 18rpx;
		padding: 0 22rpx;
		display: flex;
		align-items: center;
		justify-content: space-between;
		font-size: 28rpx;
		font-weight: 800;
	}

	.topic-row {
		display: flex;
		flex-wrap: wrap;
		gap: 14rpx;
	}

	.topic {
		padding: 14rpx 20rpx;
		border-radius: 8px;
		background: #F3E8FF;
		color: #7C3AED;
		font-size: 24rpx;
	}
</style>
