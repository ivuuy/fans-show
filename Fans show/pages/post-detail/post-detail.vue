<template>
	<view class="page">
		<view class="top-bar">
			<view class="icon-btn pressable" @click="back">‹</view>
			<text class="page-title">帖子详情</text>
			<view class="icon-btn pressable">⋯</view>
		</view>

		<view class="post card">
			<view class="post-head pressable" @click="goUser">
				<view class="avatar">{{ post.user.slice(0, 1) }}</view>
				<view>
					<text class="name">{{ post.user }}</text>
					<text class="artist pressable" @click.stop="goArtist">#{{ post.artist }}</text>
				</view>
			</view>
			<text class="content">{{ post.title }}</text>
			<view class="image-grid">
				<view v-for="item in 4" :key="item" class="image-box">{{ item }}</view>
			</view>
			<view class="actions">
				<text class="pressable" :class="{ liked }" @click="liked = !liked">♥ {{ liked ? post.likes + 1 : post.likes }}</text>
				<text>评论 {{ comments.length }}</text>
				<text class="pressable" @click="collected = !collected">{{ collected ? '已收藏' : '收藏' }}</text>
				<text>分享</text>
			</view>
		</view>

		<view class="section-head"><text class="section-title">评论</text></view>
		<view v-for="comment in comments" :key="comment" class="comment card">
			<text>{{ comment }}</text>
		</view>

		<view class="comment-bar">
			<input v-model="draft" class="comment-input" placeholder="说点什么" />
			<button class="send pressable" @click="send">发送</button>
		</view>
		<AppTabbar current="community" />
	</view>
</template>

<script>
	import AppTabbar from '@/components/app-tabbar/app-tabbar.vue'
	import { postList } from '@/data/mock.js'

	export default {
		components: { AppTabbar },
		data() {
			return {
				id: 1,
				draft: '',
				liked: false,
				collected: false,
				comments: ['这个四宫格模板太适合收藏了。', '同框感很自然，想试试毕业主题。']
			}
		},
		computed: {
			post() {
				return postList.find(item => item.id === Number(this.id)) || postList[0]
			}
		},
		onLoad(query) {
			this.id = query.id || 1
		},
		methods: {
			back() {
				uni.navigateBack()
			},
			goUser() {
				uni.navigateTo({ url: `/pages/user-home/user-home?name=${encodeURIComponent(this.post.user)}` })
			},
			goArtist() {
				uni.navigateTo({ url: `/pages/artist-detail/artist-detail?name=${encodeURIComponent(this.post.artist)}` })
			},
			send() {
				if (!this.draft) return
				this.comments.unshift(this.draft)
				this.draft = ''
			}
		}
	}
</script>

<style lang="scss" scoped>
	.top-bar {
		display: grid;
		grid-template-columns: 72rpx 1fr 72rpx;
		gap: 16rpx;
	}

	.page-title {
		text-align: center;
	}

	.post,
	.comment {
		padding: 22rpx;
		margin-bottom: 16rpx;
	}

	.post {
		display: flex;
		flex-direction: column;
		gap: 18rpx;
	}

	.post-head {
		display: flex;
		align-items: center;
		gap: 16rpx;
	}

	.avatar {
		width: 72rpx;
		height: 72rpx;
		border-radius: 50%;
		background: #F3E8FF;
		color: #7C3AED;
		display: flex;
		align-items: center;
		justify-content: center;
		font-weight: 900;
	}

	.name {
		display: block;
		font-size: 28rpx;
		font-weight: 900;
	}

	.artist {
		display: block;
		color: #7C3AED;
		font-size: 24rpx;
		margin-top: 6rpx;
	}

	.content {
		font-size: 32rpx;
		line-height: 1.6;
	}

	.image-grid {
		display: grid;
		grid-template-columns: repeat(2, 1fr);
		gap: 10rpx;
	}

	.image-box {
		height: 220rpx;
		border-radius: 8px;
		background: linear-gradient(135deg, #F3E8FF, #E0F2FE);
		display: flex;
		align-items: flex-end;
		padding: 14rpx;
		color: #7C3AED;
		font-weight: 900;
	}

	.actions {
		display: flex;
		gap: 26rpx;
		color: #6B7280;
		font-size: 24rpx;
	}

	.actions .liked {
		color: #7C3AED;
	}

	.comment {
		color: #111827;
		font-size: 26rpx;
	}

	.comment-bar {
		position: fixed;
		left: 0;
		right: 0;
		bottom: calc(112rpx + env(safe-area-inset-bottom));
		z-index: 90;
		padding: 16rpx 32rpx;
		background: #FFFFFF;
		border-top: 1rpx solid #EEF0F3;
		display: grid;
		grid-template-columns: 1fr 116rpx;
		gap: 16rpx;
	}

	.comment-input {
		height: 76rpx;
		border-radius: 8px;
		background: #F1F2F5;
		padding: 0 20rpx;
	}

	.send {
		height: 76rpx;
		border-radius: 8px;
		background: #7C3AED;
		color: #FFFFFF;
		font-size: 26rpx;
	}
</style>
