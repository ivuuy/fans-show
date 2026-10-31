<template>
	<view class="page fresh-home">
		<view class="top-shell">
			<view>
				<text class="eyebrow">Fans show</text>
				<text class="headline">今天和谁同框？</text>
			</view>
			<view class="notify pressable" @click="goMessages">
				<text>99+</text>
			</view>
		</view>

		<view class="studio-card pressable" @click="startFrame">
			<view class="studio-grid"></view>
			<view class="studio-copy">
				<text class="studio-label">FANS PHOTOBOOTH</text>
				<text class="studio-title">把今天拍成和她们同框的封面照</text>
				<text class="studio-sub">二宫格 / 四宫格 · 透明明星合成</text>
				<view class="studio-actions">
					<text>开始合拍</text>
					<text>选择模板</text>
				</view>
			</view>
			<view class="cover-stack">
				<view class="cover-photo cover-photo-back">
					<image src="/static/frame-stars/yeji.png" mode="aspectFit" />
					<text>YEJI</text>
				</view>
				<view class="cover-photo cover-photo-mid">
					<image src="/static/frame-stars/wonyoung.png" mode="aspectFit" />
					<text>WONYOUNG</text>
				</view>
				<view class="cover-photo cover-photo-front">
					<image src="/static/frame-stars/jennie.png" mode="aspectFit" />
					<text>JENNIE</text>
				</view>
			</view>
			<view class="studio-film">
				<text>02 CUT</text>
				<text>04 CUT</text>
				<text>SAVE</text>
			</view>
			<view class="studio-spark spark-a"></view>
			<view class="studio-spark spark-b"></view>
			<view class="light-ring"></view>
		</view>

		<view class="quick-row">
			<view class="quick-chip pressable" @click="goShows">
				<text class="chip-num">8</text>
				<text>行程更新</text>
			</view>
			<view class="quick-chip pressable" @click="goResale">
				<text class="chip-num">4</text>
				<text>二手周边</text>
			</view>
			<view class="quick-chip pressable" @click="goCommunity">
				<text class="chip-num">24</text>
				<text>粉丝动态</text>
			</view>
		</view>

		<view class="panel schedule-panel">
			<view class="panel-head">
				<view>
					<text class="panel-title">今日行程雷达</text>
					<text class="panel-sub">只看团、场馆和票价，不做售票</text>
				</view>
				<text class="more pressable" @click="goShows">全部</text>
			</view>
			<view v-for="show in showList.slice(0, 4)" :key="show.id" class="timeline-item pressable" @click="goShow(show.id)">
				<view class="time-dot"></view>
				<view class="timeline-main">
					<text class="timeline-title">{{ show.artist }}</text>
					<text class="timeline-desc">{{ show.name }}</text>
					<text class="timeline-meta">{{ show.date }} · {{ show.city }}</text>
				</view>
				<text class="price-pill">{{ show.price }}</text>
			</view>
		</view>

		<view class="mosaic-layout">
			<view class="panel fan-panel">
				<text class="panel-title">我关注</text>
				<view v-for="artist in followedArtistUpdates.slice(0, 4)" :key="artist.name" class="follow-line pressable" @click="goArtist(artist.name)">
					<view class="mini-logo" :style="{ background: artist.bg }">{{ artist.short }}</view>
					<view>
						<text class="follow-name">{{ artist.name }}</text>
						<text class="follow-info">{{ artist.info }}</text>
					</view>
				</view>
			</view>

			<view class="panel market-panel pressable" @click="goResale">
				<text class="panel-title">二手周边</text>
				<view class="market-card" v-for="item in resaleList.slice(0, 2)" :key="item.id">
					<text>{{ item.artist }}</text>
					<text>{{ item.price }}</text>
				</view>
				<text class="market-note">粉丝信息流</text>
			</view>
		</view>

		<view class="panel artist-panel">
			<view class="panel-head">
				<view>
					<text class="panel-title">选择团体</text>
					<text class="panel-sub">每个名字都有独立格子</text>
				</view>
				<view class="switch">
					<text :class="{ active: activeGroup === '女团' }" @click="activeGroup = '女团'">女团</text>
					<text :class="{ active: activeGroup === '男团' }" @click="activeGroup = '男团'">男团</text>
				</view>
			</view>
			<view class="artist-cloud">
				<view
					v-for="artist in filteredArtists"
					:key="artist.name"
					class="artist-bubble pressable"
					:class="{ empty: !getArtistBubbleImage(artist) }"
					@click="goArtist(artist.name)"
				>
					<view class="bubble-copy">
						<text class="bubble-name">{{ artist.name }}</text>
						<text class="bubble-member">{{ getArtistBubbleLabel(artist) }}</text>
					</view>
					<image v-if="getArtistBubbleImage(artist)" class="bubble-member-img" :src="getArtistBubbleImage(artist)" mode="aspectFit" />
				</view>
			</view>
		</view>

		<AppTabbar current="home" />
	</view>
</template>

<script>
	import AppTabbar from '@/components/app-tabbar/app-tabbar.vue'
	import { artistList, followedArtistUpdates, resaleList, showList, groupFrameAssets } from '@/data/mock.js'

	export default {
		components: { AppTabbar },
		data() {
			return {
				activeGroup: '女团',
				artistList,
				followedArtistUpdates,
				resaleList,
				showList,
				groupFrameAssets
			}
		},
		computed: {
			filteredArtists() {
				return this.artistList.filter(item => item.group === this.activeGroup)
			}
		},
		methods: {
			startFrame() {
				uni.navigateTo({ url: '/pages/frame-maker/frame-maker?layout=four' })
			},
			goMessages() {
				uni.reLaunch({ url: '/pages/messages/messages' })
			},
			goShows() {
				uni.reLaunch({ url: '/pages/shows/shows' })
			},
			goCommunity() {
				uni.reLaunch({ url: '/pages/community/community' })
			},
			goResale() {
				uni.navigateTo({ url: '/pages/resale/resale' })
			},
			goArtist(name) {
				uni.navigateTo({ url: `/pages/artist-detail/artist-detail?name=${encodeURIComponent(name)}` })
			},
			getArtistBubbleImage(artist) {
				const groupAsset = this.groupFrameAssets[artist.name]
				if (groupAsset && groupAsset.members && groupAsset.members.length) {
					return groupAsset.members[0].asset
				}
				return artist.avatar || ''
			},
			getArtistBubbleLabel(artist) {
				const groupAsset = this.groupFrameAssets[artist.name]
				if (groupAsset && groupAsset.cover) return '团体成员'
				return artist.member || artist.fans
			},
			goShow(id) {
				uni.navigateTo({ url: `/pages/show-detail/show-detail?id=${id}` })
			}
		}
	}
</script>

<style lang="scss" scoped>
	.fresh-home {
		padding: calc(var(--status-bar-height) + 28rpx) 28rpx 150rpx;
		background:
			radial-gradient(circle at 12% 0%, rgba(124, 58, 237, 0.16), transparent 30%),
			radial-gradient(circle at 90% 8%, rgba(34, 211, 238, 0.18), transparent 24%),
			#F4F5F7;
	}

	.top-shell {
		display: flex;
		align-items: center;
		justify-content: space-between;
		margin-bottom: 24rpx;
	}

	.eyebrow {
		display: block;
		color: #7C3AED;
		font-size: 24rpx;
		font-weight: 900;
		letter-spacing: 0;
	}

	.headline {
		display: block;
		margin-top: 8rpx;
		color: #0B0B11;
		font-size: 50rpx;
		font-weight: 900;
		line-height: 1.05;
	}

	.notify {
		width: 76rpx;
		height: 76rpx;
		border-radius: 24rpx;
		background: #0B0B11;
		color: #FFFFFF;
		display: flex;
		align-items: center;
		justify-content: center;
		font-size: 22rpx;
		font-weight: 900;
		box-shadow: 0 14rpx 30rpx rgba(11, 11, 17, 0.18);
	}

	.studio-card {
		position: relative;
		min-height: 462rpx;
		border-radius: 28rpx;
		padding: 30rpx;
		overflow: hidden;
		background:
			radial-gradient(circle at 74% 28%, rgba(244, 114, 182, 0.34), transparent 27%),
			radial-gradient(circle at 98% 84%, rgba(56, 189, 248, 0.28), transparent 34%),
			linear-gradient(135deg, #0A0A0D 0%, #171225 58%, #0E7490 150%);
		box-shadow: 0 28rpx 62rpx rgba(11, 11, 17, 0.24);
	}

	.studio-card::after {
		content: "";
		position: absolute;
		left: 0;
		top: 0;
		bottom: 0;
		width: 56%;
		z-index: 1;
		background: linear-gradient(90deg, rgba(10, 10, 13, 0.84), rgba(10, 10, 13, 0));
		pointer-events: none;
	}

	.studio-grid {
		position: absolute;
		inset: 0;
		opacity: 0.28;
		background:
			linear-gradient(90deg, rgba(255, 255, 255, 0.12) 0 1rpx, transparent 1rpx 46rpx),
			linear-gradient(180deg, rgba(255, 255, 255, 0.1) 0 1rpx, transparent 1rpx 46rpx);
	}

	.studio-copy {
		position: relative;
		z-index: 5;
		max-width: 336rpx;
	}

	.studio-label {
		display: inline-flex;
		padding: 10rpx 16rpx;
		border-radius: 999rpx;
		background: rgba(255, 255, 255, 0.12);
		color: #FFFFFF;
		font-size: 20rpx;
		font-weight: 900;
		border: 1rpx solid rgba(255, 255, 255, 0.16);
	}

	.studio-title {
		display: block;
		margin-top: 24rpx;
		color: #FFFFFF;
		font-size: 46rpx;
		font-weight: 900;
		line-height: 1.12;
		letter-spacing: 0;
	}

	.studio-sub {
		display: block;
		margin-top: 16rpx;
		max-width: 314rpx;
		color: rgba(255, 255, 255, 0.68);
		font-size: 23rpx;
		font-weight: 800;
		line-height: 1.36;
	}

	.studio-actions {
		display: flex;
		gap: 12rpx;
		margin-top: 24rpx;
	}

	.studio-actions text {
		padding: 14rpx 20rpx;
		border-radius: 999rpx;
		background: #FFFFFF;
		color: #0B0B11;
		font-size: 24rpx;
		font-weight: 900;
	}

	.studio-actions text:last-child {
		background: rgba(255, 255, 255, 0.12);
		color: #FFFFFF;
		border: 1rpx solid rgba(255, 255, 255, 0.16);
	}

	.cover-stack {
		position: absolute;
		right: -8rpx;
		top: 46rpx;
		width: 356rpx;
		height: 350rpx;
		z-index: 3;
	}

	.cover-photo {
		position: absolute;
		width: 166rpx;
		height: 242rpx;
		padding: 10rpx 10rpx 34rpx;
		border-radius: 12rpx;
		background: #FFFFFF;
		box-shadow: 0 22rpx 36rpx rgba(0, 0, 0, 0.3);
		transform-origin: center bottom;
	}

	.cover-photo::before {
		content: "";
		position: absolute;
		left: 10rpx;
		right: 10rpx;
		top: 10rpx;
		bottom: 52rpx;
		border-radius: 8rpx;
		background: linear-gradient(135deg, #FCE7F3, #E0F2FE);
	}

	.cover-photo image {
		position: absolute;
		left: 8rpx;
		bottom: 42rpx;
		z-index: 1;
		width: 150rpx;
		height: 186rpx;
		filter: drop-shadow(0 12rpx 8rpx rgba(0, 0, 0, 0.22));
	}

	.cover-photo text {
		position: absolute;
		left: 12rpx;
		right: 12rpx;
		bottom: 12rpx;
		z-index: 2;
		color: #111827;
		font-size: 17rpx;
		font-weight: 900;
		text-align: center;
		overflow: hidden;
		text-overflow: ellipsis;
		white-space: nowrap;
	}

	.cover-photo-back {
		left: 6rpx;
		top: 84rpx;
		transform: rotate(-14deg) scale(0.91);
		opacity: 0.88;
	}

	.cover-photo-mid {
		right: 10rpx;
		top: 28rpx;
		transform: rotate(12deg) scale(0.96);
	}

	.cover-photo-front {
		left: 96rpx;
		top: 94rpx;
		transform: rotate(-2deg);
	}

	.studio-film {
		position: absolute;
		left: 30rpx;
		right: 30rpx;
		bottom: 24rpx;
		z-index: 4;
		height: 42rpx;
		display: grid;
		grid-template-columns: repeat(3, 1fr);
		gap: 8rpx;
	}

	.studio-film text {
		border-radius: 8px;
		background: rgba(255, 255, 255, 0.1);
		color: rgba(255, 255, 255, 0.78);
		border: 1rpx solid rgba(255, 255, 255, 0.12);
		display: flex;
		align-items: center;
		justify-content: center;
		font-size: 18rpx;
		font-weight: 900;
	}

	.studio-spark {
		position: absolute;
		z-index: 2;
		width: 14rpx;
		height: 14rpx;
		background: #FFFFFF;
		transform: rotate(45deg);
		box-shadow: 0 0 24rpx rgba(255, 255, 255, 0.78);
	}

	.spark-a {
		right: 54rpx;
		top: 42rpx;
	}

	.spark-b {
		right: 316rpx;
		bottom: 96rpx;
		width: 10rpx;
		height: 10rpx;
		opacity: 0.72;
	}

	.light-ring {
		position: absolute;
		right: -92rpx;
		bottom: -110rpx;
		width: 360rpx;
		height: 360rpx;
		border-radius: 50%;
		border: 2rpx solid rgba(255, 255, 255, 0.25);
		box-shadow: inset 0 0 60rpx rgba(255, 255, 255, 0.12);
	}

	.quick-row {
		display: grid;
		grid-template-columns: repeat(3, 1fr);
		gap: 14rpx;
		margin: 18rpx 0;
	}

	.quick-chip {
		min-height: 118rpx;
		border-radius: 24rpx;
		padding: 18rpx;
		background: #FFFFFF;
		display: flex;
		flex-direction: column;
		justify-content: center;
		gap: 8rpx;
		box-shadow: 0 12rpx 30rpx rgba(17, 24, 39, 0.05);
	}

	.chip-num {
		color: #0B0B11;
		font-size: 34rpx;
		font-weight: 900;
	}

	.quick-chip text:last-child {
		color: #6B7280;
		font-size: 22rpx;
		font-weight: 800;
	}

	.panel {
		border-radius: 28rpx;
		background: #FFFFFF;
		padding: 24rpx;
		box-shadow: 0 12rpx 30rpx rgba(17, 24, 39, 0.05);
		margin-bottom: 18rpx;
	}

	.panel-head {
		display: flex;
		align-items: flex-start;
		justify-content: space-between;
		margin-bottom: 18rpx;
	}

	.panel-title {
		display: block;
		color: #0B0B11;
		font-size: 32rpx;
		font-weight: 900;
	}

	.panel-sub {
		display: block;
		margin-top: 8rpx;
		color: #6B7280;
		font-size: 22rpx;
	}

	.more {
		color: #7C3AED;
		font-size: 24rpx;
		font-weight: 900;
	}

	.timeline-item {
		display: grid;
		grid-template-columns: 28rpx 1fr auto;
		gap: 16rpx;
		align-items: center;
		padding: 18rpx 0;
		border-top: 1rpx solid #EEF0F3;
	}

	.time-dot {
		width: 18rpx;
		height: 18rpx;
		border-radius: 50%;
		background: #7C3AED;
		box-shadow: 0 0 0 8rpx #F3E8FF;
	}

	.timeline-main {
		min-width: 0;
		display: flex;
		flex-direction: column;
		gap: 6rpx;
	}

	.timeline-title {
		color: #111827;
		font-size: 28rpx;
		font-weight: 900;
	}

	.timeline-desc,
	.timeline-meta {
		color: #6B7280;
		font-size: 22rpx;
		line-height: 1.3;
	}

	.price-pill {
		max-width: 150rpx;
		padding: 10rpx 14rpx;
		border-radius: 999rpx;
		background: #F1F2F5;
		color: #111827;
		font-size: 22rpx;
		font-weight: 900;
		text-align: center;
	}

	.mosaic-layout {
		display: grid;
		grid-template-columns: 1.1fr 0.9fr;
		gap: 16rpx;
		align-items: stretch;
	}

	.fan-panel,
	.market-panel {
		margin-bottom: 18rpx;
	}

	.follow-line {
		display: flex;
		align-items: center;
		gap: 12rpx;
		margin-top: 18rpx;
	}

	.mini-logo {
		width: 54rpx;
		height: 54rpx;
		border-radius: 16rpx;
		color: #FFFFFF;
		display: flex;
		align-items: center;
		justify-content: center;
		font-size: 18rpx;
		font-weight: 900;
		border: 1rpx solid #EEF0F3;
		flex: 0 0 auto;
	}

	.follow-name,
	.follow-info {
		display: block;
	}

	.follow-name {
		color: #111827;
		font-size: 24rpx;
		font-weight: 900;
		line-height: 1.2;
	}

	.follow-info {
		margin-top: 4rpx;
		color: #6B7280;
		font-size: 19rpx;
		line-height: 1.25;
	}

	.market-panel {
		background: #111827;
		color: #FFFFFF;
	}

	.market-panel .panel-title {
		color: #FFFFFF;
	}

	.market-card {
		margin-top: 18rpx;
		padding: 18rpx;
		border-radius: 22rpx;
		background: rgba(255, 255, 255, 0.1);
		display: flex;
		flex-direction: column;
		gap: 8rpx;
	}

	.market-card text:first-child {
		font-size: 22rpx;
		font-weight: 900;
	}

	.market-card text:last-child {
		color: #A7F3D0;
		font-size: 28rpx;
		font-weight: 900;
	}

	.market-note {
		display: block;
		margin-top: 18rpx;
		color: rgba(255, 255, 255, 0.64);
		font-size: 22rpx;
	}

	.switch {
		display: flex;
		gap: 8rpx;
		padding: 6rpx;
		border-radius: 999rpx;
		background: #F1F2F5;
	}

	.switch text {
		padding: 10rpx 18rpx;
		border-radius: 999rpx;
		color: #6B7280;
		font-size: 22rpx;
		font-weight: 900;
	}

	.switch .active {
		background: #111827;
		color: #FFFFFF;
	}

	.artist-cloud {
		display: grid;
		grid-template-columns: repeat(2, minmax(0, 1fr));
		gap: 14rpx;
	}

	.artist-bubble {
		position: relative;
		min-height: 126rpx;
		padding: 18rpx 84rpx 18rpx 18rpx;
		border-radius: 24rpx;
		overflow: hidden;
		background:
			linear-gradient(135deg, rgba(255, 255, 255, 0.9), rgba(247, 248, 250, 0.82)),
			#F7F8FA;
		color: #111827;
		border: 1rpx solid #EEF0F3;
		box-shadow: inset 0 0 0 1rpx rgba(255, 255, 255, 0.8);
	}

	.artist-bubble::after {
		content: "";
		position: absolute;
		right: -18rpx;
		top: 14rpx;
		width: 118rpx;
		height: 118rpx;
		border-radius: 50%;
		background: radial-gradient(circle, rgba(124, 58, 237, 0.14), transparent 66%);
	}

	.artist-bubble.empty {
		padding-right: 18rpx;
		background: #F7F8FA;
	}

	.bubble-copy {
		position: relative;
		z-index: 2;
		min-width: 0;
		display: flex;
		flex-direction: column;
		gap: 8rpx;
	}

	.bubble-name {
		color: #111827;
		font-size: 24rpx;
		font-weight: 900;
		line-height: 1.12;
		word-break: break-word;
	}

	.bubble-member {
		color: #7C3AED;
		font-size: 18rpx;
		font-weight: 900;
		line-height: 1.1;
		overflow: hidden;
		text-overflow: ellipsis;
		white-space: nowrap;
	}

	.bubble-member-img {
		position: absolute;
		right: -6rpx;
		bottom: -4rpx;
		z-index: 1;
		width: 116rpx;
		height: 132rpx;
		filter: drop-shadow(-10rpx 12rpx 10rpx rgba(17, 24, 39, 0.2));
	}
</style>
