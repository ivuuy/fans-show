<template>
	<view class="page resale-page">
		<view class="top-bar">
			<view class="icon-btn pressable" @click="back">‹</view>
			<view class="title-wrap">
				<text class="page-title">二手周边</text>
				<text class="sub-title">粉丝发布的信息流，不做官方售卖</text>
			</view>
			<view class="icon-btn pressable" @click="publish">＋</view>
		</view>

		<view class="search pressable">搜索团名、周边、城市</view>

		<scroll-view scroll-x class="tabs" show-scrollbar="false">
			<view class="tab-row">
				<text v-for="tab in tabs" :key="tab" class="tab pressable" :class="{ active: activeTab === tab }" @click="activeTab = tab">{{ tab }}</text>
			</view>
		</scroll-view>

		<view class="notice card">
			<text>请自行核验物品状态、交易方式和对方身份。平台当前仅展示二手周边信息。</text>
		</view>

		<view class="resale-grid">
			<view v-for="item in filteredList" :key="item.id" class="resale-card card pressable" @click="openItem(item)">
				<view class="cover">
					<text>{{ item.artist }}</text>
				</view>
				<view class="item-info">
					<text class="item-title">{{ item.title }}</text>
					<text class="item-price">{{ item.price }}</text>
					<text class="muted">{{ item.condition }} · {{ item.city }}</text>
					<text class="seller">发布者 {{ item.seller }}</text>
				</view>
			</view>
		</view>

		<AppTabbar current="home" />
	</view>
</template>

<script>
	import AppTabbar from '@/components/app-tabbar/app-tabbar.vue'
	import { resaleList } from '@/data/mock.js'

	export default {
		components: { AppTabbar },
		data() {
			return {
				activeTab: '全部',
				tabs: ['全部', 'aespa', 'BABYMONSTER', 'TWS', 'RIIZE'],
				resaleList
			}
		},
		computed: {
			filteredList() {
				if (this.activeTab === '全部') return this.resaleList
				return this.resaleList.filter(item => item.artist === this.activeTab)
			}
		},
		methods: {
			back() {
				uni.navigateBack()
			},
			publish() {
				uni.showToast({ title: '发布二手周边信息', icon: 'none' })
			},
			openItem(item) {
				uni.showToast({ title: `${item.title} · ${item.price}`, icon: 'none' })
			}
		}
	}
</script>

<style lang="scss" scoped>
	.top-bar {
		display: grid;
		grid-template-columns: 72rpx 1fr 72rpx;
		gap: 16rpx;
		align-items: center;
	}

	.title-wrap {
		text-align: center;
	}

	.page-title {
		display: block;
	}

	.sub-title {
		display: block;
		margin-top: 6rpx;
		color: #6B7280;
		font-size: 22rpx;
	}

	.search {
		height: 82rpx;
		padding: 0 24rpx;
		border-radius: 8px;
		background: #F1F2F5;
		color: #9AA0A6;
		display: flex;
		align-items: center;
		font-size: 26rpx;
		margin-bottom: 20rpx;
	}

	.tabs {
		white-space: nowrap;
		margin-bottom: 18rpx;
	}

	.tab-row {
		display: flex;
		gap: 14rpx;
	}

	.tab {
		padding: 16rpx 28rpx;
		border-radius: 34rpx;
		background: #FFFFFF;
		border: 2rpx solid #E8EBEF;
		color: #111827;
		font-size: 26rpx;
		font-weight: 800;
	}

	.tab.active {
		background: #111827;
		color: #FFFFFF;
		border-color: #111827;
	}

	.notice {
		padding: 20rpx;
		margin-bottom: 20rpx;
		color: #6B7280;
		font-size: 24rpx;
		line-height: 1.45;
	}

	.resale-grid {
		display: grid;
		grid-template-columns: repeat(2, 1fr);
		gap: 18rpx;
	}

	.resale-card {
		overflow: hidden;
	}

	.cover {
		height: 210rpx;
		background: linear-gradient(135deg, #111827, #7C3AED, #ECFEFF);
		display: flex;
		align-items: flex-end;
		padding: 18rpx;
		color: #FFFFFF;
		font-size: 28rpx;
		font-weight: 900;
	}

	.item-info {
		padding: 18rpx;
		display: flex;
		flex-direction: column;
		gap: 8rpx;
	}

	.item-title {
		color: #111827;
		font-size: 26rpx;
		font-weight: 900;
		line-height: 1.35;
	}

	.item-price {
		color: #7C3AED;
		font-size: 30rpx;
		font-weight: 900;
	}

	.seller {
		color: #6B7280;
		font-size: 22rpx;
	}
</style>
