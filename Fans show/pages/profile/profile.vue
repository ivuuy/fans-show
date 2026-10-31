<template>
	<view class="page profile-page">
		<view class="profile-hero" :style="heroStyle">
			<view class="hero-overlay"></view>
			<view class="hero-top">
				<view class="hero-avatar-wrap">
					<view class="hero-avatar">
						<image v-if="draftAvatarImage || avatarImage" class="hero-avatar-img" :src="draftAvatarImage || avatarImage" mode="aspectFill" />
						<text v-else>{{ avatarInitial }}</text>
					</view>
					<view class="hero-plus pressable" @click="chooseAvatar">+</view>
				</view>

				<view class="hero-copy">
					<view class="hero-name-row">
						<text class="hero-name">{{ draftName || displayName }}</text>
						<view class="hero-drop">▾</view>
					</view>
					<text class="hero-sub">抖音风个人主页 · 可自定义封面和资料</text>
					<text class="hero-id">ID：{{ profileId }}</text>
					<view class="hero-actions">
						<view class="hero-action pressable" @click="openEditProfile">编辑主页</view>
					</view>
				</view>
			</view>
		</view>

		<view class="profile-card card">
			<view class="stats-row">
				<view v-for="item in stats" :key="item.label" class="stat-item">
					<text class="stat-num">{{ item.value }}</text>
					<text class="stat-label">{{ item.label }}</text>
				</view>
			</view>

			<text class="bio-text">{{ profileBio }}</text>

			<view class="tag-row">
				<text class="tag">{{ profileRegionType }} · {{ profileCity }}</text>
				<text class="tag">{{ profileBirthday }}</text>
				<text class="tag">{{ profileFavorite }}</text>
			</view>
		</view>

		<view v-if="showEditProfile" class="edit-panel card">
			<view class="panel-head">
				<text class="panel-title">编辑资料</text>
				<text class="muted">主页会实时更新</text>
			</view>

			<view class="field">
				<text class="field-label">昵称</text>
				<input class="field-input" v-model="draftName" maxlength="16" placeholder="输入昵称" />
			</view>
			<view class="field">
				<text class="field-label">签名</text>
				<input class="field-input" v-model="draftBio" maxlength="40" placeholder="写一句介绍自己" />
			</view>
			<view class="field">
				<text class="field-label">所在地方</text>
				<picker class="picker" :range="regionTypes" :value="draftRegionIndex" @change="changeRegionType">
					<view class="picker-value">{{ draftRegionType }}</view>
				</picker>
			</view>
			<view class="field">
				<text class="field-label">具体地点</text>
				<picker class="picker" :range="activeCityOptions" :value="draftCityIndex" @change="changeCity">
					<view class="picker-value">{{ draftCity }}</view>
				</picker>
			</view>
			<view class="field">
				<text class="field-label">生日</text>
				<input class="field-input" v-model="draftBirthday" maxlength="12" placeholder="例如 05.17" />
			</view>
			<view class="field">
				<text class="field-label">标签</text>
				<input class="field-input" v-model="draftFavorite" maxlength="24" placeholder="例如 aespa / Karina" />
			</view>

			<view class="panel-actions">
				<view class="ghost-btn pressable" @click="cancelEditProfile">取消</view>
				<view class="save-btn pressable" @click="saveProfile">完成</view>
			</view>
		</view>

		<AppTabbar current="profile" />
	</view>
</template>

<script>
	import AppTabbar from '@/components/app-tabbar/app-tabbar.vue'

	export default {
		components: { AppTabbar },
		data() {
			return {
				profileId: 'FS20260517',
				displayName: 'Fans show 用户',
				avatarImage: '',
				profileBio: '我开始认识时间是一个圆，它滚动的轨迹最终会覆盖开始时让人不舍的那一部分。',
				profileRegionType: '中国',
				profileCity: '上海',
				profileBirthday: '05.17',
				profileFavorite: '女团 · aespa',
				draftName: 'Fans show 用户',
				draftAvatarImage: '',
				draftBio: '我开始认识时间是一个圆，它滚动的轨迹最终会覆盖开始时让人不舍的那一部分。',
				draftRegionType: '中国',
				draftCity: '上海',
				draftBirthday: '05.17',
				draftFavorite: '女团 · aespa',
				regionTypes: ['中国', '海外'],
				cityGroups: {
					'中国': ['上海', '北京', '广州', '深圳', '杭州', '成都', '重庆', '南京'],
					'海外': ['首尔', '东京', '大阪', '纽约', '洛杉矶', '伦敦', '巴黎', '新加坡']
				},
				showEditProfile: false,
				stats: [
					{ label: '获赞', value: '0' },
					{ label: '互关', value: '0' },
					{ label: '关注', value: '0' },
					{ label: '粉丝', value: '0' }
				]
			}
		},
		computed: {
			avatarInitial() {
				return (this.displayName || 'F').slice(0, 1).toUpperCase()
			},
			activeCityOptions() {
				return this.cityGroups[this.draftRegionType] || this.cityGroups['中国']
			},
			draftRegionIndex() {
				const index = this.regionTypes.indexOf(this.draftRegionType)
				return index >= 0 ? index : 0
			},
			draftCityIndex() {
				const index = this.activeCityOptions.indexOf(this.draftCity)
				return index >= 0 ? index : 0
			},
			heroStyle() {
				if (this.avatarImage) return { backgroundImage: 'linear-gradient(180deg, rgba(17,24,39,0.18), rgba(17,24,39,0.72))' }
				return {}
			}
		},
		onLoad() {
			this.loadProfile()
		},
		methods: {
			loadProfile() {
				const saved = uni.getStorageSync('fansShowProfile')
				if (!saved) return
				this.displayName = saved.displayName || this.displayName
				this.avatarImage = saved.avatarImage || ''
				this.profileBio = saved.profileBio || this.profileBio
				this.profileRegionType = saved.profileRegionType || this.profileRegionType
				this.profileCity = saved.profileCity || this.profileCity
				this.profileBirthday = saved.profileBirthday || this.profileBirthday
				this.profileFavorite = saved.profileFavorite || this.profileFavorite
				this.syncDraft()
			},
			syncDraft() {
				this.draftName = this.displayName
				this.draftAvatarImage = this.avatarImage
				this.draftBio = this.profileBio
				this.draftRegionType = this.profileRegionType
				this.draftCity = this.profileCity
				this.draftBirthday = this.profileBirthday
				this.draftFavorite = this.profileFavorite
			},
			openEditProfile() {
				this.syncDraft()
				this.showEditProfile = true
			},
			cancelEditProfile() {
				this.syncDraft()
				this.showEditProfile = false
			},
			getCityOptions(regionType) {
				return this.cityGroups[regionType] || this.cityGroups['中国']
			},
			changeRegionType(event) {
				this.draftRegionType = this.regionTypes[Number(event.detail.value)] || '中国'
				this.draftCity = this.getCityOptions(this.draftRegionType)[0]
			},
			changeCity(event) {
				this.draftCity = this.activeCityOptions[Number(event.detail.value)] || this.draftCity
			},
			chooseAvatar() {
				uni.chooseImage({
					count: 1,
					sourceType: ['album', 'camera'],
					success: res => {
						this.draftAvatarImage = res.tempFilePaths[0]
						if (!this.showEditProfile) this.avatarImage = res.tempFilePaths[0]
					},
					fail: () => uni.showToast({ title: '无法选择头像', icon: 'none' })
				})
			},
			clearAvatar() {
				this.avatarImage = ''
				this.draftAvatarImage = ''
			},
			saveProfile() {
				const nextName = this.draftName.trim()
				if (!nextName) {
					uni.showToast({ title: '请输入昵称', icon: 'none' })
					return
				}
				this.displayName = nextName
				this.avatarImage = this.draftAvatarImage
				this.profileBio = this.draftBio.trim()
				this.profileRegionType = this.draftRegionType
				this.profileCity = this.draftCity
				this.profileBirthday = this.draftBirthday.trim()
				this.profileFavorite = this.draftFavorite.trim()
				uni.setStorageSync('fansShowProfile', {
					displayName: this.displayName,
					avatarImage: this.avatarImage,
					profileBio: this.profileBio,
					profileRegionType: this.profileRegionType,
					profileCity: this.profileCity,
					profileBirthday: this.profileBirthday,
					profileFavorite: this.profileFavorite
				})
				this.showEditProfile = false
				uni.showToast({ title: '已保存', icon: 'none' })
			}
		}
	}
</script>

<style lang="scss">
	.profile-page {
		padding-bottom: 160rpx;
		background: #F4F5F7;
	}

	.profile-hero {
		position: relative;
		min-height: 390rpx;
		padding: 34rpx 28rpx 28rpx;
		border-radius: 0 0 40rpx 40rpx;
		background: linear-gradient(180deg, #9B9CA0, #7D7D83);
		background-size: cover;
		background-position: center;
		overflow: hidden;
	}

	.hero-overlay {
		position: absolute;
		inset: 0;
		background:
			radial-gradient(circle at 18rpx 18rpx, rgba(255, 255, 255, 0.4) 0 10rpx, transparent 11rpx) 0 0/78rpx 78rpx,
			linear-gradient(180deg, rgba(255, 255, 255, 0.12), rgba(0, 0, 0, 0.24));
	}

	.hero-top,
	.profile-card,
	.board-grid,
	.edit-panel {
		position: relative;
		z-index: 1;
	}

	.hero-top {
		display: flex;
		align-items: flex-start;
		gap: 22rpx;
	}

	.hero-avatar-wrap {
		position: relative;
		width: 240rpx;
		height: 240rpx;
		flex: 0 0 auto;
	}

	.hero-avatar {
		width: 100%;
		height: 100%;
		border-radius: 50%;
		border: 6rpx solid rgba(255, 255, 255, 0.92);
		background: rgba(255, 255, 255, 0.14);
		display: flex;
		align-items: center;
		justify-content: center;
		overflow: hidden;
		color: #FFFFFF;
		font-size: 88rpx;
		font-weight: 900;
		box-shadow: 0 18rpx 40rpx rgba(0, 0, 0, 0.22);
	}

	.hero-avatar-img {
		width: 100%;
		height: 100%;
	}

	.hero-plus {
		position: absolute;
		right: 14rpx;
		bottom: 12rpx;
		width: 62rpx;
		height: 62rpx;
		border-radius: 50%;
		background: #4ADE80;
		color: #FFFFFF;
		font-size: 52rpx;
		font-weight: 900;
		display: flex;
		align-items: center;
		justify-content: center;
		box-shadow: 0 10rpx 24rpx rgba(0, 0, 0, 0.16);
	}

	.hero-copy {
		flex: 1;
		min-width: 0;
		padding-top: 36rpx;
		color: #FFFFFF;
	}

	.hero-name-row {
		display: flex;
		align-items: center;
		gap: 12rpx;
	}

	.hero-name {
		font-size: 40rpx;
		font-weight: 900;
		line-height: 1.1;
	}

	.hero-drop {
		width: 34rpx;
		height: 34rpx;
		border-radius: 50%;
		background: rgba(255, 255, 255, 0.18);
		display: flex;
		align-items: center;
		justify-content: center;
		font-size: 20rpx;
	}

	.hero-sub,
	.hero-id {
		display: block;
		margin-top: 12rpx;
		color: rgba(255, 255, 255, 0.84);
		font-size: 22rpx;
		line-height: 1.4;
	}

	.hero-actions {
		margin-top: 24rpx;
	}

	.hero-action {
		display: inline-flex;
		align-items: center;
		justify-content: center;
		height: 70rpx;
		padding: 0 26rpx;
		border-radius: 999rpx;
		background: rgba(255, 255, 255, 0.16);
		color: #FFFFFF;
		font-size: 25rpx;
		font-weight: 900;
	}

	.profile-card {
		margin: -18rpx 24rpx 0;
		padding: 26rpx 24rpx 22rpx;
		background: #FFFFFF;
		border-radius: 28rpx;
		box-shadow: 0 16rpx 34rpx rgba(17, 24, 39, 0.08);
	}

	.stats-row {
		display: grid;
		grid-template-columns: repeat(4, 1fr);
		gap: 10rpx;
		text-align: center;
	}

	.stat-item {
		display: flex;
		flex-direction: column;
		gap: 8rpx;
	}

	.stat-num {
		font-size: 36rpx;
		font-weight: 900;
		color: #111827;
	}

	.stat-label {
		font-size: 22rpx;
		color: #6B7280;
	}

	.bio-text {
		display: block;
		margin-top: 24rpx;
		color: #111827;
		font-size: 28rpx;
		line-height: 1.75;
	}

	.tag-row {
		display: flex;
		flex-wrap: wrap;
		gap: 12rpx;
		margin-top: 22rpx;
	}

	.tag {
		padding: 12rpx 16rpx;
		border-radius: 10rpx;
		background: #F1F3F6;
		color: #555C66;
		font-size: 22rpx;
		font-weight: 700;
	}

	.board-grid {
		display: grid;
		grid-template-columns: repeat(2, 1fr);
		gap: 16rpx;
		padding: 16rpx 24rpx 0;
	}

	.board-card {
		padding: 22rpx;
	}

	.board-title {
		display: block;
		font-size: 28rpx;
		font-weight: 900;
		color: #111827;
	}

	.board-desc {
		display: block;
		margin-top: 8rpx;
		color: #6B7280;
		font-size: 22rpx;
	}

	.mini-actions {
		display: flex;
		gap: 12rpx;
		margin-top: 18rpx;
	}

	.mini-btn {
		flex: 1;
		height: 58rpx;
		border-radius: 14rpx;
		background: #111827;
		color: #FFFFFF;
		font-size: 23rpx;
		font-weight: 800;
		display: flex;
		align-items: center;
		justify-content: center;
	}

	.edit-panel {
		margin: 18rpx 24rpx 0;
		padding: 22rpx;
		background: #FFFFFF;
		border-radius: 24rpx;
		box-shadow: 0 16rpx 34rpx rgba(17, 24, 39, 0.06);
	}

	.panel-head {
		display: flex;
		align-items: baseline;
		justify-content: space-between;
		margin-bottom: 14rpx;
	}

	.panel-title {
		font-size: 30rpx;
		font-weight: 900;
		color: #111827;
	}

	.field {
		display: flex;
		align-items: center;
		gap: 18rpx;
		min-height: 84rpx;
		border-top: 1rpx solid #EEF0F3;
	}

	.field-label {
		width: 132rpx;
		flex: 0 0 132rpx;
		font-size: 24rpx;
		font-weight: 800;
		color: #374151;
	}

	.field-input,
	.picker {
		flex: 1;
		min-width: 0;
		height: 72rpx;
	}

	.field-input {
		font-size: 25rpx;
		font-weight: 800;
		color: #111827;
	}

	.picker-value {
		height: 72rpx;
		display: flex;
		align-items: center;
		justify-content: space-between;
		font-size: 25rpx;
		font-weight: 800;
		color: #111827;
	}

	.picker-value::after {
		content: '›';
		color: #9AA0A6;
		font-size: 34rpx;
	}

	.panel-actions {
		display: flex;
		gap: 14rpx;
		margin-top: 18rpx;
	}

	.ghost-btn,
	.save-btn {
		flex: 1;
		height: 68rpx;
		border-radius: 16rpx;
		display: flex;
		align-items: center;
		justify-content: center;
		font-size: 25rpx;
		font-weight: 900;
	}

	.ghost-btn {
		background: #F3F4F6;
		color: #374151;
	}

	.save-btn {
		background: #111827;
		color: #FFFFFF;
	}
</style>
