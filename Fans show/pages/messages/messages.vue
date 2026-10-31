<template>
	<view class="page dm-page">
		<view class="dm-hero">
			<view class="hero-top">
				<view>
					<text class="hero-title">DM</text>
					<text class="hero-subtitle">最近的互动、提醒和系统消息</text>
				</view>
				<view class="hero-badge">{{ unreadCount }} 未读</view>
			</view>

			<view class="quick-row">
				<view
					v-for="item in quickActions"
					:key="item.label"
					class="quick-item pressable"
					@click="setCategory(item.target)"
				>
					<view class="quick-avatar" :class="item.tone">
						<text>{{ item.icon }}</text>
						<view v-if="item.badge" class="quick-badge">{{ item.badge }}</view>
					</view>
					<text class="quick-label">{{ item.label }}</text>
					<text class="quick-meta">{{ item.meta }}</text>
				</view>
			</view>
		</view>

		<scroll-view class="chip-scroll" scroll-x show-scrollbar="false">
			<view class="chip-row">
				<view
					v-for="item in categories"
					:key="item.label"
					class="filter-chip pressable"
					:class="{ active: activeCategory === item.label }"
					@click="setCategory(item.label)"
				>
					<text>{{ item.label }}</text>
					<text class="chip-count">{{ item.count }}</text>
				</view>
			</view>
		</scroll-view>

		<view class="message-stack">
			<view
				v-for="msg in filteredMessages"
				:key="msg.title"
				class="message-row pressable"
				@click="openMessage(msg)"
			>
				<view class="message-avatar" :class="toneClass(msg.type)">
					<text class="message-avatar-icon">{{ msg.type.slice(0, 1) }}</text>
				</view>
				<view class="message-main">
					<view class="message-head">
						<text class="message-title">{{ msg.title }}</text>
						<text class="message-time">{{ msg.time }}</text>
					</view>
					<text class="message-desc">{{ msg.desc }}</text>
				</view>
				<view v-if="msg.unread" class="message-dot"></view>
			</view>
		</view>

		<AppTabbar current="messages" />
	</view>
</template>

<script>
	import AppTabbar from '@/components/app-tabbar/app-tabbar.vue'
	import { messageList } from '@/data/mock.js'

	export default {
		components: { AppTabbar },
		data() {
			return {
				messageList,
				activeCategory: '全部'
			}
		},
		computed: {
			unreadCount() {
				return this.messageList.filter(item => item.unread).length
			},
			categories() {
				return [
					{ label: '全部', count: this.messageList.length },
					{ label: '互动', count: this.messageList.filter(item => item.type === '互动').length },
					{ label: '系统通知', count: this.messageList.filter(item => item.type === '系统通知').length }
				]
			},
			filteredMessages() {
				if (this.activeCategory === '全部') return this.messageList
				return this.messageList.filter(item => item.type === this.activeCategory)
			},
			quickActions() {
				return [
					{
						label: '互动',
						icon: '♥',
						target: '互动',
						meta: '点赞评论',
						badge: this.messageList.filter(item => item.type === '互动' && item.unread).length || '',
						tone: 'tone-pink'
					},
					{
						label: '通知',
						icon: '●',
						target: '系统通知',
						meta: '系统消息',
						badge: this.messageList.filter(item => item.type === '系统通知' && item.unread).length || '',
						tone: 'tone-blue'
					},
					{
						label: '全部',
						icon: '◌',
						target: '全部',
						meta: '全部会话',
						badge: this.unreadCount || '',
						tone: 'tone-white'
					}
				]
			}
		},
		methods: {
			setCategory(label) {
				this.activeCategory = label
			},
			toneClass(type) {
				if (type === '互动') return 'tone-pink'
				if (type === '行程') return 'tone-gold'
				return 'tone-blue'
			},
			openMessage(msg) {
				if (msg.type === '系统通知') {
					uni.navigateTo({ url: '/pages/show-detail/show-detail?id=1' })
					return
				}
				uni.navigateTo({ url: '/pages/post-detail/post-detail?id=1' })
			}
		}
	}
</script>

<style lang="scss">
	.dm-page {
		background:
			radial-gradient(circle at top right, rgba(255, 116, 178, 0.24), transparent 28%),
			radial-gradient(circle at top left, rgba(255, 186, 73, 0.18), transparent 26%),
			linear-gradient(180deg, #0E1014 0, #171A20 320rpx, #F5F6F8 320rpx, #F5F6F8 100%);
	}

	.dm-hero {
		margin: 8rpx -8rpx 18rpx;
		padding: 10rpx 8rpx 0;
	}

	.hero-top {
		display: flex;
		align-items: flex-start;
		justify-content: space-between;
		gap: 20rpx;
	}

	.hero-title {
		display: block;
		color: #FFFFFF;
		font-size: 52rpx;
		font-weight: 900;
		line-height: 1;
	}

	.hero-subtitle {
		display: block;
		margin-top: 14rpx;
		color: rgba(255, 255, 255, 0.68);
		font-size: 24rpx;
		line-height: 1.35;
	}

	.hero-badge {
		flex: 0 0 auto;
		padding: 12rpx 18rpx;
		border-radius: 999rpx;
		background: rgba(255, 255, 255, 0.12);
		backdrop-filter: blur(16rpx);
		color: #FFFFFF;
		font-size: 22rpx;
		font-weight: 900;
	}

	.quick-row {
		margin-top: 28rpx;
		display: grid;
		grid-template-columns: repeat(4, minmax(0, 1fr));
		gap: 14rpx;
	}

	.quick-item {
		padding: 18rpx 8rpx 10rpx;
		display: flex;
		flex-direction: column;
		align-items: center;
		gap: 10rpx;
	}

	.quick-avatar {
		position: relative;
		width: 98rpx;
		height: 98rpx;
		border-radius: 50%;
		display: flex;
		align-items: center;
		justify-content: center;
		font-size: 34rpx;
		font-weight: 900;
		box-shadow: 0 16rpx 32rpx rgba(0, 0, 0, 0.16);
	}

	.quick-badge {
		position: absolute;
		top: -4rpx;
		right: -4rpx;
		min-width: 34rpx;
		height: 34rpx;
		padding: 0 8rpx;
		border-radius: 999rpx;
		background: #F43F5E;
		color: #FFFFFF;
		font-size: 18rpx;
		font-weight: 900;
		display: flex;
		align-items: center;
		justify-content: center;
		border: 4rpx solid #171A20;
	}

	.quick-label {
		color: #FFFFFF;
		font-size: 24rpx;
		font-weight: 800;
		line-height: 1;
	}

	.quick-meta {
		color: rgba(255, 255, 255, 0.56);
		font-size: 20rpx;
		line-height: 1;
	}

	.chip-scroll {
		margin: 0 -32rpx;
		white-space: nowrap;
	}

	.chip-row {
		display: inline-flex;
		gap: 14rpx;
		padding: 0 32rpx 8rpx;
	}

	.filter-chip {
		flex: 0 0 auto;
		height: 62rpx;
		padding: 0 22rpx;
		border-radius: 999rpx;
		background: #FFFFFF;
		color: #374151;
		display: inline-flex;
		align-items: center;
		gap: 10rpx;
		font-size: 23rpx;
		font-weight: 800;
		box-shadow: 0 8rpx 18rpx rgba(17, 24, 39, 0.05);
	}

	.filter-chip.active {
		background: #111827;
		color: #FFFFFF;
	}

	.chip-count {
		color: #9AA0A6;
		font-size: 20rpx;
		font-weight: 900;
	}

	.filter-chip.active .chip-count {
		color: rgba(255, 255, 255, 0.72);
	}

	.message-stack {
		margin-top: 12rpx;
		padding-bottom: 20rpx;
	}

	.message-row {
		position: relative;
		display: flex;
		align-items: center;
		gap: 18rpx;
		padding: 22rpx 8rpx;
		border-bottom: 1rpx solid rgba(17, 24, 39, 0.06);
	}

	.message-avatar {
		width: 88rpx;
		height: 88rpx;
		border-radius: 50%;
		display: flex;
		align-items: center;
		justify-content: center;
		flex: 0 0 auto;
		box-shadow: 0 12rpx 24rpx rgba(17, 24, 39, 0.08);
	}

	.message-avatar-icon {
		font-size: 32rpx;
		font-weight: 900;
	}

	.tone-pink {
		background: linear-gradient(135deg, #FFE4EE, #FFC0D6);
		color: #BE185D;
	}

	.tone-blue {
		background: linear-gradient(135deg, #DBEAFE, #BFDBFE);
		color: #1D4ED8;
	}

	.tone-white {
		background: linear-gradient(135deg, #FFFFFF, #E5E7EB);
		color: #111827;
	}

	.message-main {
		flex: 1;
		min-width: 0;
		display: flex;
		flex-direction: column;
		gap: 8rpx;
	}

	.message-head {
		display: flex;
		align-items: center;
		justify-content: space-between;
		gap: 18rpx;
	}

	.message-title {
		flex: 1;
		min-width: 0;
		color: #111827;
		font-size: 28rpx;
		font-weight: 900;
		overflow: hidden;
		text-overflow: ellipsis;
		white-space: nowrap;
	}

	.message-time {
		flex: 0 0 auto;
		color: #9AA0A6;
		font-size: 21rpx;
	}

	.message-desc {
		color: #6B7280;
		font-size: 23rpx;
		line-height: 1.35;
		overflow: hidden;
		text-overflow: ellipsis;
		white-space: nowrap;
	}

	.message-dot {
		width: 14rpx;
		height: 14rpx;
		border-radius: 50%;
		background: #F43F5E;
		flex: 0 0 auto;
	}
</style>
