<template>
	<view class="page">
		<view class="top-bar">
			<view>
				<text class="page-title">行程</text>
				<text class="sub-title">所有团的行程与票价信息</text>
			</view>
			<view class="icon-btn pressable" @click="goSearch">⌕</view>
		</view>

		<view class="filters">
			<view class="filter card pressable" @click="toast('城市筛选')">上海</view>
			<view class="filter card pressable" @click="toast('日期筛选')">本月</view>
			<view class="filter card pressable" @click="toast('艺人筛选')">艺人</view>
		</view>

		<view class="search pressable" @click="goSearch">搜索团名、城市、行程、票价</view>

		<scroll-view scroll-x class="date-scroll">
			<view class="date-row">
				<text v-for="item in dates" :key="item" class="date-chip pressable" :class="{ active: activeDate === item }" @click="activeDate = item">{{ item }}</text>
			</view>
		</scroll-view>

		<view v-for="show in showList" :key="show.id" class="show-card card pressable" @click="goDetail(show.id)">
			<view class="poster"></view>
			<view class="show-main">
				<text class="show-title">{{ show.name }}</text>
				<text class="artist" @click.stop="goArtist(show.artist)">{{ show.artist }}</text>
				<text class="muted">{{ show.date }}</text>
				<text class="muted">{{ show.city }} · {{ show.venue }}</text>
				<view class="meta-row">
					<text class="tag">{{ show.status }}</text>
					<text class="price">票价 {{ show.price }}</text>
				</view>
			</view>
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
				showList,
				activeDate: '全部',
				dates: ['今天', '本周', '本月', '全部']
			}
		},
		methods: {
			toast(title) {
				uni.showToast({ title, icon: 'none' })
			},
			goSearch() {
				uni.navigateTo({ url: '/pages/search/search' })
			},
			goDetail(id) {
				uni.navigateTo({ url: `/pages/show-detail/show-detail?id=${id}` })
			},
			goArtist(name) {
				uni.navigateTo({ url: `/pages/artist-detail/artist-detail?name=${encodeURIComponent(name)}` })
			}
		}
	}
</script>

<style lang="scss" scoped>
	.filters {
		display: grid;
		grid-template-columns: repeat(3, 1fr);
		gap: 14rpx;
	}

	.sub-title {
		display: block;
		margin-top: 6rpx;
		color: #6B7280;
		font-size: 24rpx;
	}

	.filter {
		height: 72rpx;
		display: flex;
		align-items: center;
		justify-content: center;
		color: #111827;
		font-size: 26rpx;
		font-weight: 700;
	}

	.search {
		height: 80rpx;
		margin-top: 18rpx;
		padding: 0 24rpx;
		border-radius: 8px;
		background: #F1F2F5;
		display: flex;
		align-items: center;
		color: #9AA0A6;
		font-size: 26rpx;
	}

	.date-scroll {
		margin: 24rpx 0;
		white-space: nowrap;
	}

	.date-row {
		display: flex;
		gap: 14rpx;
	}

	.date-chip {
		padding: 16rpx 26rpx;
		border-radius: 8px;
		background: #FFFFFF;
		color: #6B7280;
		font-size: 26rpx;
	}

	.date-chip.active {
		background: #7C3AED;
		color: #FFFFFF;
	}

	.show-card {
		display: flex;
		gap: 20rpx;
		padding: 18rpx;
		margin-bottom: 18rpx;
	}

	.poster {
		width: 184rpx;
		height: 248rpx;
		border-radius: 8px;
		background: linear-gradient(145deg, #111827, #7C3AED, #06B6D4);
		flex: 0 0 auto;
	}

	.show-main {
		flex: 1;
		display: flex;
		flex-direction: column;
		gap: 10rpx;
		align-items: flex-start;
	}

	.show-title {
		font-size: 30rpx;
		font-weight: 900;
		line-height: 1.3;
	}

	.artist {
		color: #7C3AED;
		font-size: 26rpx;
		font-weight: 800;
	}

	.meta-row {
		display: flex;
		flex-wrap: wrap;
		gap: 10rpx;
		align-items: center;
	}

	.price {
		padding: 10rpx 18rpx;
		border-radius: 8px;
		background: #F1F2F5;
		color: #111827;
		font-size: 22rpx;
		font-weight: 800;
	}
</style>
