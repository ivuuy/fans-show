<template>
	<view class="page detail-page">
		<view class="poster-hero">
			<view class="cover-actions">
				<view class="icon-btn pressable" @click="back">‹</view>
				<view class="icon-btn pressable" @click="collected = !collected">{{ collected ? '♥' : '♡' }}</view>
			</view>
		</view>
		<view class="info card">
			<text class="show-title">{{ show.name }}</text>
			<text class="artist pressable" @click="goArtist">{{ show.artist }}</text>
			<text class="muted">{{ show.date }}</text>
			<text class="muted">{{ show.city }} · {{ show.venue }}</text>
			<text class="price">票价 {{ show.price }}</text>
		</view>

		<view class="section-head"><text class="section-title">行程信息</text></view>
		<view class="schedule-grid">
			<view class="schedule-card card">
				<text class="schedule-label">类型</text>
				<text class="schedule-value">{{ show.category }}</text>
			</view>
			<view class="schedule-card card">
				<text class="schedule-label">状态</text>
				<text class="schedule-value">{{ show.status }}</text>
			</view>
			<view class="schedule-card card">
				<text class="schedule-label">城市</text>
				<text class="schedule-value">{{ show.city }}</text>
			</view>
			<view class="schedule-card card">
				<text class="schedule-label">票价</text>
				<text class="schedule-value">{{ show.price }}</text>
			</view>
		</view>

		<view class="notice card">
			<text class="notice-title">信息说明</text>
			<text class="muted">本页只整理行程、场馆与票价信息，不提供官方售票。</text>
			<text class="muted">票价、开票时间和场馆可能变动，请以官方公告为准。</text>
			<text class="muted">关注后可在消息页接收该团行程更新提醒。</text>
		</view>

		<view class="buy-bar">
			<view class="bar-action pressable" @click="collected = !collected">{{ collected ? '已收藏' : '收藏' }}</view>
			<view class="bar-action pressable" @click="share">分享</view>
			<button class="primary-btn pressable buy-btn" @click="followSchedule">{{ followed ? '已订阅提醒' : '订阅行程提醒' }}</button>
		</view>
		<AppTabbar current="shows" />
	</view>
</template>

<script>
	import AppTabbar from '@/components/app-tabbar/app-tabbar.vue'
	import { showList } from '@/data/mock.js'

	export default {
		components: { AppTabbar },
		data() {
			return {
				id: 1,
				collected: false,
				followed: false
			}
		},
		computed: {
			show() {
				return showList.find(item => item.id === Number(this.id)) || showList[0]
			}
		},
		onLoad(query) {
			this.id = query.id || 1
		},
		methods: {
			back() {
				uni.navigateBack()
			},
			share() {
				uni.showToast({ title: '已打开分享面板', icon: 'none' })
			},
			goArtist() {
				uni.navigateTo({ url: `/pages/artist-detail/artist-detail?name=${encodeURIComponent(this.show.artist)}` })
			},
			followSchedule() {
				this.followed = !this.followed
				uni.showToast({ title: this.followed ? '已订阅行程提醒' : '已取消提醒', icon: 'none' })
			}
		}
	}
</script>

<style lang="scss" scoped>
	.detail-page {
		padding-left: 0;
		padding-right: 0;
	}

	.poster-hero {
		height: 520rpx;
		padding: calc(var(--status-bar-height) + 18rpx) 32rpx 0;
		background: linear-gradient(145deg, #111827, #7C3AED 58%, #06B6D4);
	}

	.cover-actions {
		display: flex;
		justify-content: space-between;
	}

	.info,
	.schedule-grid,
	.notice,
	.section-head {
		margin-left: 32rpx;
		margin-right: 32rpx;
	}

	.info {
		margin-top: -54rpx;
		padding: 26rpx;
		display: flex;
		flex-direction: column;
		gap: 10rpx;
	}

	.show-title {
		font-size: 36rpx;
		font-weight: 900;
		line-height: 1.28;
	}

	.artist,
	.price {
		color: #7C3AED;
		font-size: 28rpx;
		font-weight: 900;
	}

	.schedule-grid {
		display: grid;
		grid-template-columns: repeat(2, 1fr);
		gap: 16rpx;
	}

	.schedule-card {
		padding: 20rpx;
		display: flex;
		flex-direction: column;
		gap: 8rpx;
	}

	.schedule-label {
		color: #6B7280;
		font-size: 24rpx;
	}

	.schedule-value {
		color: #111827;
		font-size: 28rpx;
		font-weight: 900;
	}

	.notice {
		margin-top: 24rpx;
		padding: 24rpx;
		display: flex;
		flex-direction: column;
		gap: 12rpx;
	}

	.notice-title {
		font-size: 30rpx;
		font-weight: 900;
	}

	.buy-bar {
		position: fixed;
		left: 0;
		right: 0;
		bottom: calc(112rpx + env(safe-area-inset-bottom));
		z-index: 90;
		padding: 16rpx 32rpx;
		background: #FFFFFF;
		border-top: 1rpx solid #EEF0F3;
		display: grid;
		grid-template-columns: 92rpx 92rpx 1fr;
		gap: 16rpx;
		align-items: center;
	}

	.bar-action {
		color: #6B7280;
		font-size: 24rpx;
		text-align: center;
	}

	.buy-btn {
		height: 86rpx;
	}
</style>
