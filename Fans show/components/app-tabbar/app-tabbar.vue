<template>
	<view class="tabbar">
		<view
			v-for="item in tabs"
			:key="item.key"
			class="tabbar-item pressable"
			:class="{ active: current === item.key }"
			@click="go(item)"
		>
			<text class="tabbar-icon">{{ item.icon }}</text>
			<text class="tabbar-label">{{ item.label }}</text>
		</view>
	</view>
</template>

<script>
	export default {
		name: 'AppTabbar',
		props: {
			current: {
				type: String,
				default: 'home'
			}
		},
		data() {
			return {
				tabs: [
					{ key: 'home', label: '主页', icon: 'w', path: '/pages/index/index' },
					{ key: 'shows', label: '行程', icon: '▢', path: '/pages/shows/shows' },
					{ key: 'messages', label: '消息', icon: '⌁', path: '/pages/messages/messages' },
					{ key: 'profile', label: '我', icon: '•••', path: '/pages/profile/profile' }
				]
			}
		},
		methods: {
			go(item) {
				if (item.key === this.current) return
				uni.switchTab({
					url: item.path,
					fail: () => {
						uni.reLaunch({ url: item.path })
					}
				})
			}
		}
	}
</script>

<style lang="scss" scoped>
	.tabbar {
		position: fixed;
		left: 0;
		right: 0;
		bottom: 0;
		z-index: 99;
		height: calc(112rpx + env(safe-area-inset-bottom));
		padding: 8rpx 16rpx env(safe-area-inset-bottom);
		display: grid;
		grid-template-columns: repeat(4, 1fr);
		background: #FFFFFF;
		border-top: 1rpx solid rgba(238, 240, 243, 0.78);
		box-shadow: 0 -8rpx 24rpx rgba(17, 24, 39, 0.04);
	}

	.tabbar-item {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
		gap: 6rpx;
		color: #333333;
	}

	.tabbar-item.active {
		color: #111111;
	}

	.tabbar-icon {
		min-width: 54rpx;
		height: 54rpx;
		border-radius: 50%;
		display: flex;
		align-items: center;
		justify-content: center;
		font-size: 38rpx;
		font-weight: 900;
		line-height: 1;
	}

	.tabbar-item.active .tabbar-icon {
		background: #333333;
		color: #FFFFFF;
	}

	.tabbar-label {
		font-size: 24rpx;
		font-weight: 700;
		line-height: 1;
	}
</style>
