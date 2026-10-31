<template>
	<view class="page">
		<view class="top-bar">
			<text class="page-title">社区</text>
			<view class="icon-btn pressable" @click="goPublish">＋</view>
		</view>

		<view class="tabs">
			<text v-for="tab in tabs" :key="tab" class="pressable" :class="{ active: activeTab === tab }" @click="activeTab = tab">{{ tab }}</text>
		</view>

		<scroll-view scroll-x class="topic-scroll">
			<view class="topic-row">
				<text v-for="topic in topics" :key="topic" class="topic pressable">{{ topic }}</text>
			</view>
		</scroll-view>

		<view v-for="post in postList" :key="post.id" class="post card pressable" @click="goPost(post.id)">
			<view class="post-head">
				<view class="avatar pressable" @click.stop="goUser(post.user)">{{ post.user.slice(0, 1) }}</view>
				<view class="post-user">
					<text class="name">{{ post.user }}</text>
					<text class="artist pressable" @click.stop="goArtist(post.artist)">#{{ post.artist }}</text>
				</view>
			</view>
			<text class="content">{{ post.title }}</text>
			<view class="image-grid">
				<view v-for="item in 4" :key="item" class="image-box"></view>
			</view>
			<view class="actions">
				<text class="pressable" @click.stop="toggleLike(post.id)">♥ {{ liked[post.id] ? post.likes + 1 : post.likes }}</text>
				<text>评论 {{ post.comments }}</text>
				<text>收藏</text>
			</view>
		</view>

		<view class="float-btn pressable" @click="goPublish">＋</view>
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
				postList,
				activeTab: '推荐',
				tabs: ['推荐', '关注', '应援', '现场', '二创'],
				topics: ['#aespa回归', '#RIIZE舞台', '#IVE现场', '#四宫格合拍', '#人生照片'],
				liked: {}
			}
		},
		methods: {
			toggleLike(id) {
				this.$set ? this.$set(this.liked, id, !this.liked[id]) : (this.liked[id] = !this.liked[id])
			},
			goPost(id) {
				uni.navigateTo({ url: `/pages/post-detail/post-detail?id=${id}` })
			},
			goUser(name) {
				uni.navigateTo({ url: `/pages/user-home/user-home?name=${encodeURIComponent(name)}` })
			},
			goArtist(name) {
				uni.navigateTo({ url: `/pages/artist-detail/artist-detail?name=${encodeURIComponent(name)}` })
			},
			goPublish() {
				uni.navigateTo({ url: '/pages/publish/publish' })
			}
		}
	}
</script>

<style lang="scss" scoped>
	.tabs {
		display: flex;
		gap: 24rpx;
		margin-bottom: 20rpx;
	}

	.tabs text {
		padding-bottom: 12rpx;
		color: #6B7280;
		font-size: 28rpx;
		font-weight: 800;
	}

	.tabs .active {
		color: #7C3AED;
		border-bottom: 4rpx solid #7C3AED;
	}

	.topic-scroll {
		white-space: nowrap;
		margin-bottom: 20rpx;
	}

	.topic-row {
		display: flex;
		gap: 14rpx;
	}

	.topic {
		padding: 14rpx 20rpx;
		border-radius: 8px;
		background: #F3E8FF;
		color: #7C3AED;
		font-size: 24rpx;
	}

	.post {
		padding: 22rpx;
		margin-bottom: 18rpx;
		display: flex;
		flex-direction: column;
		gap: 16rpx;
	}

	.post-head {
		display: flex;
		gap: 16rpx;
		align-items: center;
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

	.post-user {
		display: flex;
		flex-direction: column;
		gap: 6rpx;
	}

	.name {
		font-size: 28rpx;
		font-weight: 900;
	}

	.artist {
		color: #7C3AED;
		font-size: 24rpx;
	}

	.content {
		font-size: 30rpx;
		line-height: 1.55;
		color: #111827;
	}

	.image-grid {
		display: grid;
		grid-template-columns: repeat(2, 1fr);
		gap: 10rpx;
	}

	.image-box {
		height: 180rpx;
		border-radius: 8px;
		background: linear-gradient(135deg, #F3E8FF, #E0F2FE);
	}

	.actions {
		display: flex;
		gap: 32rpx;
		color: #6B7280;
		font-size: 24rpx;
	}

	.float-btn {
		position: fixed;
		right: 34rpx;
		bottom: calc(148rpx + env(safe-area-inset-bottom));
		width: 96rpx;
		height: 96rpx;
		border-radius: 50%;
		background: #7C3AED;
		color: #FFFFFF;
		display: flex;
		align-items: center;
		justify-content: center;
		font-size: 44rpx;
		font-weight: 700;
		box-shadow: 0 18rpx 34rpx rgba(124, 58, 237, 0.28);
	}
</style>
