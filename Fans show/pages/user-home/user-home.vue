<template>
	<view class="page user-page">
		<view class="cover">
			<view class="icon-btn pressable" @click="back">‹</view>
		</view>
		<view class="user-card card">
			<view class="avatar">{{ name.slice(0, 1) }}</view>
			<view class="user-row">
				<view>
					<text class="user-name">{{ name }}</text>
					<text class="muted">喜欢收藏和爱豆同框的人生四宫格</text>
				</view>
				<button class="follow pressable" :class="{ followed }" @click="followed = !followed">{{ followed ? '已关注' : '关注' }}</button>
			</view>
			<view class="stats">
				<view><text>128</text><text class="muted">关注</text></view>
				<view><text>3.2万</text><text class="muted">粉丝</text></view>
				<view><text>18万</text><text class="muted">获赞</text></view>
			</view>
		</view>
		<view class="tabs">
			<text class="active">动态</text><text>收藏</text><text>应援</text>
		</view>
		<view v-for="post in postList" :key="post.id" class="post-card card pressable" @click="goPost(post.id)">
			<text>{{ post.title }}</text>
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
			return { name: 'Mina', followed: false, postList }
		},
		onLoad(query) {
			if (query.name) this.name = decodeURIComponent(query.name)
		},
		methods: {
			back() { uni.navigateBack() },
			goPost(id) { uni.navigateTo({ url: `/pages/post-detail/post-detail?id=${id}` }) }
		}
	}
</script>

<style lang="scss" scoped>
	.user-page {
		padding-left: 0;
		padding-right: 0;
	}

	.cover {
		height: 300rpx;
		padding: calc(var(--status-bar-height) + 18rpx) 32rpx;
		background: linear-gradient(135deg, #111827, #7C3AED, #06B6D4);
	}

	.user-card,
	.tabs,
	.post-card {
		margin-left: 32rpx;
		margin-right: 32rpx;
	}

	.user-card {
		margin-top: -58rpx;
		padding: 24rpx;
	}

	.avatar {
		width: 144rpx;
		height: 144rpx;
		margin-top: -78rpx;
		border-radius: 50%;
		border: 6rpx solid #FFFFFF;
		background: #F3E8FF;
		color: #7C3AED;
		display: flex;
		align-items: center;
		justify-content: center;
		font-size: 48rpx;
		font-weight: 900;
	}

	.user-row {
		display: flex;
		align-items: center;
		justify-content: space-between;
		gap: 18rpx;
		margin-top: 16rpx;
	}

	.user-name {
		display: block;
		font-size: 36rpx;
		font-weight: 900;
		margin-bottom: 8rpx;
	}

	.follow {
		width: 128rpx;
		height: 64rpx;
		border-radius: 8px;
		background: #7C3AED;
		color: #FFFFFF;
	}

	.follow.followed {
		background: #111827;
	}

	.stats {
		display: grid;
		grid-template-columns: repeat(3, 1fr);
		margin-top: 24rpx;
		text-align: center;
	}

	.stats view {
		display: flex;
		flex-direction: column;
		gap: 8rpx;
		font-weight: 900;
	}

	.tabs {
		display: flex;
		gap: 28rpx;
		margin-top: 28rpx;
	}

	.tabs text {
		padding-bottom: 12rpx;
		color: #6B7280;
		font-weight: 800;
	}

	.tabs .active {
		color: #7C3AED;
		border-bottom: 4rpx solid #7C3AED;
	}

	.post-card {
		margin-top: 16rpx;
		padding: 22rpx;
		font-size: 28rpx;
		line-height: 1.5;
	}
</style>
