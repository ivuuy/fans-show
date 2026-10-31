<template>
	<view class="page artist-page">
		<view class="cover">
			<image v-if="artistCover" class="cover-photo" :src="artistCover" mode="aspectFill" />
			<view class="cover-actions">
				<view class="icon-btn pressable" @click="back">‹</view>
				<view class="icon-btn pressable" @click="share">↗</view>
			</view>
			<text class="cover-text">{{ artistName }}</text>
		</view>
		<view class="profile-card card">
			<view class="avatar">
				<image v-if="artistAvatar" :src="artistAvatar" mode="aspectFill" />
				<text v-else>{{ artistName.slice(0, 1) }}</text>
			</view>
			<view class="profile-main">
				<text class="name">{{ artistName }}</text>
				<text class="muted">{{ artist ? artist.fans : '128万' }} 粉丝 · 团体成员合拍四宫格</text>
			</view>
			<button class="follow pressable" :class="{ followed }" @click="followed = !followed">{{ followed ? '已关注' : '关注' }}</button>
		</view>

		<view class="frame-action card">
			<view v-if="artistMemberImage" class="frame-member">
				<image :src="artistMemberImage" mode="aspectFit" />
			</view>
			<view class="frame-copy">
				<text class="frame-title">和 {{ artistName }} 成员合拍人生四宫格</text>
				<text class="frame-desc">上传照片，生成同框毕业照、旅行照、舞台照和纪念照。</text>
			</view>
			<button class="primary-btn pressable small-btn" @click="goFrame">生成</button>
		</view>

		<view class="stats card">
			<view><text class="stat-num">128</text><text class="muted">动态</text></view>
			<view><text class="stat-num">24</text><text class="muted">演出</text></view>
			<view><text class="stat-num">8.6万</text><text class="muted">应援</text></view>
		</view>

		<view class="tabs">
			<text v-for="tab in tabs" :key="tab" :class="{ active: activeTab === tab }" @click="activeTab = tab">{{ tab }}</text>
		</view>

		<view v-if="activeTab === '动态'">
			<view v-for="post in relatedPosts" :key="post.id" class="post-card card pressable" @click="goPost(post.id)">
				<text class="post-title">{{ post.title }}</text>
				<text class="muted">{{ post.likes }} 赞 · {{ post.comments }} 评论</text>
			</view>
		</view>
		<view v-else-if="activeTab === '演出'">
			<view v-for="show in relatedShows" :key="show.id" class="show-card card pressable" @click="goShow(show.id)">
				<text class="post-title">{{ show.name }}</text>
				<text class="muted">{{ show.date }} · {{ show.city }} · {{ show.status }}</text>
			</view>
		</view>
		<view v-else class="works-grid">
			<view v-for="item in 6" :key="item" class="work-card card pressable">四宫格作品 {{ item }}</view>
		</view>

		<AppTabbar current="home" />
	</view>
</template>

<script>
	import AppTabbar from '@/components/app-tabbar/app-tabbar.vue'
	import { artistList, showList, postList, groupFrameAssets } from '@/data/mock.js'

	export default {
		components: { AppTabbar },
		data() {
			return {
				artistName: 'aespa',
				followed: false,
				activeTab: '动态',
				tabs: ['动态', '演出', '粉丝作品']
			}
		},
		computed: {
			artist() {
				return artistList.find(item => item.name === this.artistName)
			},
			groupAsset() {
				return groupFrameAssets[this.artistName] || {}
			},
			artistCover() {
				return this.groupAsset.cover || ''
			},
			artistAvatar() {
				return this.artistCover || (this.artist && this.artist.avatar ? this.artist.avatar : '')
			},
			artistMemberImage() {
				const members = this.groupAsset.members || []
				if (members.length) return members[0].asset
				return this.artist && this.artist.avatar ? this.artist.avatar : ''
			},
			relatedPosts() {
				const matched = postList.filter(item => item.artist === this.artistName)
				return matched.length ? matched : postList
			},
			relatedShows() {
				const matched = showList.filter(item => item.artist === this.artistName)
				return matched.length ? matched : showList
			}
		},
		onLoad(query) {
			if (query.name) this.artistName = decodeURIComponent(query.name)
		},
		methods: {
			back() {
				uni.navigateBack()
			},
			share() {
				uni.showToast({ title: '已打开分享面板', icon: 'none' })
			},
			goShow(id) {
				uni.navigateTo({ url: `/pages/show-detail/show-detail?id=${id}` })
			},
			goPost(id) {
				uni.navigateTo({ url: `/pages/post-detail/post-detail?id=${id}` })
			},
			goFrame() {
				uni.navigateTo({ url: `/pages/frame-maker/frame-maker?artist=${encodeURIComponent(this.artistName)}&layout=four` })
			}
		}
	}
</script>

<style lang="scss" scoped>
	.artist-page {
		padding-left: 0;
		padding-right: 0;
	}

	.cover {
		position: relative;
		height: 440rpx;
		padding: calc(var(--status-bar-height) + 18rpx) 32rpx 32rpx;
		background: linear-gradient(135deg, #111827, #7C3AED 60%, #06B6D4);
		display: flex;
		flex-direction: column;
		justify-content: space-between;
		overflow: hidden;
	}

	.cover::after {
		content: "";
		position: absolute;
		inset: 0;
		z-index: 1;
		background: linear-gradient(180deg, rgba(17, 24, 39, 0.28), rgba(17, 24, 39, 0.74));
	}

	.cover-actions {
		position: relative;
		z-index: 4;
		display: flex;
		justify-content: space-between;
	}

	.cover-text {
		position: relative;
		z-index: 4;
		color: #FFFFFF;
		font-size: 52rpx;
		font-weight: 900;
	}

	.cover-photo {
		position: absolute;
		inset: 0;
		z-index: 0;
		width: 100%;
		height: 100%;
	}

	.profile-card,
	.frame-action,
	.stats,
	.tabs,
	.post-card,
	.show-card,
	.works-grid {
		margin-left: 32rpx;
		margin-right: 32rpx;
	}

	.profile-card {
		position: relative;
		z-index: 5;
		margin-top: 24rpx;
		padding: 22rpx;
		display: flex;
		align-items: center;
		gap: 18rpx;
		background: #FFFFFF;
	}

	.avatar {
		position: relative;
		width: 92rpx;
		height: 92rpx;
		border-radius: 50%;
		background: #F3E8FF;
		color: #7C3AED;
		display: flex;
		align-items: center;
		justify-content: center;
		font-size: 36rpx;
		font-weight: 900;
		border: 4rpx solid #FFFFFF;
		overflow: hidden;
	}

	.avatar image {
		width: 92rpx;
		height: 92rpx;
	}

	.profile-main {
		flex: 1;
		display: flex;
		flex-direction: column;
		gap: 8rpx;
	}

	.name,
	.frame-title {
		font-size: 32rpx;
		font-weight: 900;
		color: #111827;
	}

	.follow {
		width: 112rpx;
		height: 60rpx;
		border-radius: 8px;
		background: #7C3AED;
		color: #FFFFFF;
		font-size: 24rpx;
	}

	.follow.followed {
		background: #111827;
	}

	.frame-action {
		position: relative;
		margin-top: 24rpx;
		padding: 22rpx;
		display: flex;
		gap: 18rpx;
		align-items: center;
		justify-content: space-between;
		overflow: hidden;
	}

	.frame-copy {
		flex: 1;
		min-width: 0;
	}

	.frame-member {
		width: 144rpx;
		height: 118rpx;
		border-radius: 18rpx;
		background: linear-gradient(135deg, #F3E8FF, #E0F2FE);
		flex: 0 0 auto;
		overflow: hidden;
	}

	.frame-member image {
		width: 144rpx;
		height: 118rpx;
	}

	.frame-desc {
		display: block;
		margin-top: 8rpx;
		color: #6B7280;
		font-size: 24rpx;
		line-height: 1.45;
	}

	.small-btn {
		width: 148rpx;
		flex: 0 0 148rpx;
		height: 68rpx;
		font-size: 24rpx;
		white-space: nowrap;
		display: flex;
		align-items: center;
		justify-content: center;
	}

	.stats {
		margin-top: 24rpx;
		padding: 24rpx;
		display: grid;
		grid-template-columns: repeat(3, 1fr);
		text-align: center;
	}

	.stats view {
		display: flex;
		flex-direction: column;
		gap: 6rpx;
	}

	.stat-num {
		font-size: 32rpx;
		font-weight: 900;
	}

	.tabs {
		margin-top: 28rpx;
		display: flex;
		gap: 24rpx;
	}

	.tabs text {
		padding-bottom: 12rpx;
		font-size: 28rpx;
		color: #6B7280;
		font-weight: 700;
	}

	.tabs .active {
		color: #7C3AED;
		border-bottom: 4rpx solid #7C3AED;
	}

	.post-card,
	.show-card {
		margin-top: 16rpx;
		padding: 22rpx;
		display: flex;
		flex-direction: column;
		gap: 10rpx;
	}

	.post-title {
		font-size: 28rpx;
		font-weight: 800;
		line-height: 1.42;
	}

	.works-grid {
		margin-top: 16rpx;
		display: grid;
		grid-template-columns: repeat(2, 1fr);
		gap: 16rpx;
	}

	.work-card {
		height: 220rpx;
		padding: 18rpx;
		background: linear-gradient(135deg, #FFFFFF, #F3E8FF);
		color: #111827;
		font-weight: 800;
	}
</style>
