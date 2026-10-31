<template>
	<view class="page">
		<view class="search-top">
			<view class="icon-btn pressable" @click="back">‹</view>
			<input class="search-input" v-model="keyword" :placeholder="placeholder" focus />
			<text class="cancel pressable" @click="back">取消</text>
		</view>

		<view v-if="!keyword">
			<view class="section-head"><text class="section-title">搜索历史</text></view>
			<view class="tag-row">
				<text v-for="item in history" :key="item" class="chip pressable" @click="keyword = item">{{ item }}</text>
			</view>
			<view class="section-head"><text class="section-title">热门搜索</text></view>
			<view class="tag-row">
				<text v-for="item in hotWords" :key="item" class="chip hot pressable" @click="keyword = item">{{ item }}</text>
			</view>
		</view>

		<view v-else>
			<view class="section-head"><text class="section-title">艺人</text></view>
			<view v-for="artist in artistResults" :key="artist.name" class="result-card card pressable" @click="goArtist(artist.name)">
				<view class="avatar">{{ artist.name.slice(0, 1) }}</view>
				<view class="result-main">
					<text class="result-title">{{ artist.name }}</text>
					<text class="muted">{{ artist.fans }} 粉丝 · 热度 {{ artist.heat }}</text>
				</view>
				<button class="follow-btn">关注</button>
			</view>

			<view class="section-head"><text class="section-title">演出</text></view>
			<view v-for="show in showResults" :key="show.id" class="simple-card card pressable" @click="goShow(show.id)">
				<text class="result-title">{{ show.name }}</text>
				<text class="muted">{{ show.date }} · {{ show.city }} · {{ show.status }}</text>
			</view>

			<view class="section-head"><text class="section-title">帖子</text></view>
			<view v-for="post in postResults" :key="post.id" class="simple-card card pressable" @click="goPost(post.id)">
				<text class="result-title">{{ post.title }}</text>
				<text class="muted">{{ post.user }} · {{ post.likes }} 赞</text>
			</view>
		</view>

		<AppTabbar current="home" />
	</view>
</template>

<script>
	import AppTabbar from '@/components/app-tabbar/app-tabbar.vue'
	import { artistList, showList, postList, frameStarList } from '@/data/mock.js'

	export default {
		components: { AppTabbar },
		data() {
			return {
				keyword: '',
				mode: '',
				history: ['aespa', 'RIIZE', 'IVE', 'Stray Kids'],
				hotWords: ['四宫格合拍模板', 'BABYMONSTER', 'NewJeans', 'ENHYPEN'],
				artistList,
				frameStarList,
				showList,
				postList
			}
		},
		computed: {
			placeholder() {
				return this.mode === 'frame' ? '选择想合拍人生四宫格的爱豆' : '搜索艺人、演出、帖子'
			},
			lowerKey() {
				return this.keyword.toLowerCase()
			},
			artistResults() {
				return this.artistList.filter(item => item.name.toLowerCase().includes(this.lowerKey)).slice(0, 8)
			},
			showResults() {
				return this.showList.filter(item => `${item.name}${item.artist}${item.city}`.toLowerCase().includes(this.lowerKey)).slice(0, 4)
			},
			postResults() {
				return this.postList.filter(item => `${item.title}${item.artist}${item.user}`.toLowerCase().includes(this.lowerKey)).slice(0, 4)
			}
		},
		onLoad(query) {
			this.mode = query.mode || ''
		},
		methods: {
			back() {
				uni.navigateBack()
			},
			goArtist(name) {
				if (this.mode === 'frame') {
					const lowerName = name.toLowerCase()
					const artist = this.artistList.find(item => item.name.toLowerCase() === lowerName && item.avatar)
					if (artist) {
						uni.navigateTo({ url: `/pages/frame-maker/frame-maker?artist=${encodeURIComponent(artist.name)}` })
						return
					}

					const frameStar = this.frameStarList.find(item => {
						const starName = item.name.toLowerCase()
						const groupName = item.group.toLowerCase()
						return starName === lowerName || starName.includes(lowerName) || groupName === lowerName
					})

					if (frameStar) {
						uni.navigateTo({ url: `/pages/frame-maker/frame-maker?star=${frameStar.id}` })
						return
					}
				}

				uni.navigateTo({ url: `/pages/artist-detail/artist-detail?name=${encodeURIComponent(name)}&mode=${this.mode}` })
			},
			goShow(id) {
				uni.navigateTo({ url: `/pages/show-detail/show-detail?id=${id}` })
			},
			goPost(id) {
				uni.navigateTo({ url: `/pages/post-detail/post-detail?id=${id}` })
			}
		}
	}
</script>

<style lang="scss" scoped>
	.search-top {
		display: grid;
		grid-template-columns: 72rpx 1fr 76rpx;
		align-items: center;
		gap: 16rpx;
		margin-bottom: 24rpx;
	}

	.search-input {
		height: 80rpx;
		border-radius: 8px;
		background: #F1F2F5;
		padding: 0 24rpx;
		font-size: 28rpx;
		color: #111827;
	}

	.cancel {
		color: #6B7280;
		font-size: 26rpx;
		text-align: right;
	}

	.tag-row {
		display: flex;
		flex-wrap: wrap;
		gap: 16rpx;
	}

	.chip {
		padding: 16rpx 22rpx;
		border-radius: 8px;
		background: #F1F2F5;
		color: #111827;
		font-size: 26rpx;
	}

	.chip.hot {
		background: #F3E8FF;
		color: #7C3AED;
	}

	.result-card,
	.simple-card {
		margin-bottom: 16rpx;
		padding: 20rpx;
		display: flex;
		align-items: center;
		gap: 18rpx;
	}

	.simple-card {
		align-items: flex-start;
		flex-direction: column;
	}

	.avatar {
		width: 76rpx;
		height: 76rpx;
		border-radius: 50%;
		background: #F3E8FF;
		color: #7C3AED;
		display: flex;
		align-items: center;
		justify-content: center;
		font-weight: 800;
	}

	.result-main {
		flex: 1;
		display: flex;
		flex-direction: column;
		gap: 8rpx;
	}

	.result-title {
		font-size: 28rpx;
		font-weight: 800;
		color: #111827;
		line-height: 1.35;
	}

	.follow-btn {
		width: 104rpx;
		height: 56rpx;
		border-radius: 8px;
		background: #7C3AED;
		color: #FFFFFF;
		font-size: 24rpx;
	}
</style>
