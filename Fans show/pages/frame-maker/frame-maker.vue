<template>
	<view class="page frame-page" :style="{ '--star-accent': selectedStar.accent }">
		<view class="maker-top">
			<view class="icon-btn pressable" @click="back">‹</view>
			<view class="top-copy">
				<text class="top-title">明星合影</text>
				<text class="top-sub">{{ layoutLabel }} · {{ selectedStar.name }}</text>
			</view>
			<view class="top-pill">{{ userImages.length }}/4</view>
		</view>

		<view class="preview-shell">
			<view class="preview-stage" :class="layoutClass">
				<view v-for="(tile, index) in previewTiles" :key="index" class="preview-tile">
					<image v-if="tile.userImage" class="user-photo" :src="tile.userImage" mode="aspectFill" />
					<view v-else class="empty-photo" @click="chooseImages">
						<text>＋</text>
						<text>添加照片</text>
					</view>
					<image v-if="tile.member" class="star-cutout group-cutout" :src="tile.member.asset" mode="aspectFit" />
					<view v-if="tile.withStar" class="name-strip">
						<text>{{ tile.member ? tile.member.name : selectedStar.name }}</text>
						<text>{{ selectedStar.group }}</text>
					</view>
				</view>
			</view>
		</view>

		<view class="control-card card">
			<view class="section-line">
				<text class="control-title">模板</text>
				<view class="layout-switch">
					<text class="pressable" :class="{ active: layout === 'two' }" @click="setLayout('two')">二宫格</text>
					<text class="pressable" :class="{ active: layout === 'four' }" @click="setLayout('four')">四宫格</text>
				</view>
			</view>

			<view class="section-line">
				<text class="control-title">照片</text>
				<button class="mini-btn pressable" @click="chooseImages">相册/拍照</button>
			</view>

			<view class="thumb-grid">
				<view v-for="(image, index) in userImages" :key="image" class="photo-thumb">
					<image :src="image" mode="aspectFill" />
					<text @click.stop="removeImage(index)">×</text>
				</view>
				<view v-if="userImages.length < 4" class="photo-thumb add pressable" @click="chooseImages">＋</view>
			</view>
		</view>

		<view class="star-section card">
			<view class="section-line compact">
				<text class="control-title">选择团体成员</text>
				<text class="muted">{{ selectedStar.pose }}</text>
			</view>
			<scroll-view scroll-x class="star-scroll" show-scrollbar="false">
				<view class="star-row">
					<view
						v-for="star in selectableStars"
						:key="star.id"
						class="star-chip pressable"
						:class="{ active: selectedStar.id === star.id }"
						@click="selectStar(star.id)"
					>
						<view class="star-avatar" :style="{ backgroundColor: star.accent }">
							<image :src="star.asset" mode="aspectFit" />
						</view>
						<text class="star-name">{{ star.name }}</text>
						<text class="star-group">{{ star.group }}</text>
					</view>
				</view>
			</scroll-view>
		</view>

		<view class="bottom-actions">
			<button class="secondary-btn pressable" @click="chooseImages">换照片</button>
			<button class="save-btn pressable" :class="{ disabled: !canGenerate }" @click="generatePoster">
				{{ generating ? '生成中' : '生成保存' }}
			</button>
		</view>

		<canvas canvas-id="frameCanvas" id="frameCanvas" class="export-canvas" />
	</view>
</template>

<script>
	import { artistList, frameStarList, groupFrameAssets } from '@/data/mock.js'

	const CANVAS_SIZE = 1080

	export default {
		data() {
			return {
				layout: 'four',
				selectedStarId: 'artist-aespa',
				userImages: [],
				generating: false,
				frameStarList,
				artistList,
				groupFrameAssets
			}
		},
		computed: {
			groupFrameStars() {
				return this.artistList
					.filter(item => item.group === '女团' && (this.groupFrameAssets[item.name] || item.avatar))
					.map((item, index) => ({
						id: `artist-${this.slugify(item.name)}`,
						name: item.name,
						group: item.name,
						asset: this.resolveGroupMembers(item)[0].asset,
						cover: this.resolveGroupAsset(item).cover || '',
						members: this.resolveGroupMembers(item),
						accent: this.pickAccent(index),
						pose: this.resolveGroupMembers(item).length > 1 || this.resolveGroupAsset(item).cover ? '团体成员合拍' : '团体对应成员'
					}))
			},
			selectableStars() {
				const existingIds = new Set(this.groupFrameStars.map(item => item.id))
				const extraStars = this.frameStarList
					.filter(item => !existingIds.has(item.id))
					.map(item => ({ ...item, pose: item.pose || 'solo' }))
				return this.groupFrameStars.concat(extraStars)
			},
			selectedStar() {
				return this.selectableStars.find(item => item.id === this.selectedStarId) || this.selectableStars[0]
			},
			layoutLabel() {
				return this.layout === 'two' ? '二宫格' : '四宫格'
			},
			layoutClass() {
				return this.layout === 'two' ? 'layout-two' : 'layout-four'
			},
			canGenerate() {
				return this.userImages.length > 0 && !this.generating
			},
			previewTiles() {
				const count = this.layout === 'two' ? 2 : 4
				return Array.from({ length: count }, (_, index) => ({
					userImage: this.getLoopImage(index),
					withStar: this.layout === 'two' ? index === 1 : true,
					member: (this.layout === 'two' ? index === 1 : true) ? this.getLoopMember(index) : null
				}))
			}
		},
		onLoad(query) {
			if (query.layout === 'two' || query.layout === 'four') {
				this.layout = query.layout
			}

			if (query.artist) {
				const artistName = decodeURIComponent(query.artist)
				const artist = this.artistList.find(item => item.name === artistName) ||
					this.artistList.find(item => item.name.toLowerCase() === artistName.toLowerCase())
				if (artist && artist.avatar) {
					this.selectedStarId = `artist-${this.slugify(artist.name)}`
					return
				}
			}

			if (query.star && this.selectableStars.some(item => item.id === query.star)) {
				this.selectedStarId = query.star
			}
		},
		methods: {
			slugify(value) {
				return String(value).replace(/[^A-Za-z0-9]+/g, '-').replace(/^-|-$/g, '')
			},
			pickAccent(index) {
				const accents = ['#7C3AED', '#E11D48', '#0891B2', '#F97316', '#111827', '#059669', '#DB2777', '#2563EB']
				return accents[index % accents.length]
			},
			resolveGroupAsset(artist) {
				return this.groupFrameAssets[artist.name] || {}
			},
			resolveGroupMembers(artist) {
				const groupAsset = this.resolveGroupAsset(artist)
				if (groupAsset.members && groupAsset.members.length) return groupAsset.members
				if (artist.avatar) return [{ name: artist.member || artist.name, asset: artist.avatar }]
				return []
			},
			getLoopMember(index) {
				const members = this.selectedStar.members && this.selectedStar.members.length
					? this.selectedStar.members
					: [{ name: this.selectedStar.name, asset: this.selectedStar.asset }]
				return members[index % members.length]
			},
			back() {
				uni.navigateBack()
			},
			setLayout(layout) {
				this.layout = layout
			},
			selectStar(id) {
				this.selectedStarId = id
			},
			getLoopImage(index) {
				if (!this.userImages.length) return ''
				return this.userImages[index % this.userImages.length]
			},
			chooseImages() {
				const remain = Math.max(1, 4 - this.userImages.length)
				uni.chooseImage({
					count: remain,
					sizeType: ['compressed'],
					sourceType: ['album', 'camera'],
					success: res => {
						const paths = res.tempFilePaths || []
						this.userImages = this.userImages.concat(paths).slice(0, 4)
					},
					fail: () => {
						uni.showToast({ title: '没有选择照片', icon: 'none' })
					}
				})
			},
			removeImage(index) {
				this.userImages.splice(index, 1)
			},
			async generatePoster() {
				if (!this.userImages.length) {
					uni.showToast({ title: '先添加一张照片', icon: 'none' })
					return
				}

				if (this.generating) return

				this.generating = true
				uni.showLoading({ title: '生成中' })

				try {
					await this.drawPoster()
					const tempPath = await this.canvasToImage()
					await this.saveImage(tempPath)
					uni.hideLoading()
					uni.showToast({ title: '已保存到相册', icon: 'success' })
				} catch (error) {
					uni.hideLoading()
					const tempPath = error && error.tempFilePath
					if (tempPath) {
						uni.previewImage({ urls: [tempPath] })
						uni.showToast({ title: '保存失败，已打开预览', icon: 'none' })
					} else {
						uni.showToast({ title: '生成失败，请重试', icon: 'none' })
					}
				} finally {
					this.generating = false
				}
			},
			async drawPoster() {
				const imageInfoMap = await this.prepareImageInfo()
				const memberImages = await this.prepareMemberImages()

				return new Promise((resolve, reject) => {
					const ctx = uni.createCanvasContext('frameCanvas', this)
					const tiles = this.getCanvasTiles()

					ctx.setFillStyle('#111827')
					ctx.fillRect(0, 0, CANVAS_SIZE, CANVAS_SIZE)
					ctx.setFillStyle('#FFFFFF')
					ctx.fillRect(28, 28, CANVAS_SIZE - 56, CANVAS_SIZE - 56)

					tiles.forEach((tile, index) => {
						const userImage = this.getLoopImage(index)
						const member = this.getLoopMember(index)
						const memberImage = memberImages[member.asset]
						this.drawTile(ctx, tile, index, userImage, imageInfoMap[userImage], member, memberImage)
					})

					ctx.setFillStyle(this.selectedStar.accent)
					ctx.fillRect(44, CANVAS_SIZE - 108, CANVAS_SIZE - 88, 52)
					ctx.setFillStyle('#FFFFFF')
					ctx.setFontSize(28)
					ctx.fillText(`${this.layoutLabel}  ${this.selectedStar.name} / ${this.selectedStar.group}`, 66, CANVAS_SIZE - 73)

					ctx.draw(false, () => {
						setTimeout(resolve, 160)
					})
				})
			},
			drawTile(ctx, tile, index, imagePath, imageInfo, member, memberImage) {
				ctx.save()
				ctx.beginPath()
				ctx.rect(tile.x, tile.y, tile.w, tile.h)
				ctx.clip()
				ctx.setFillStyle(index % 2 ? '#E5E7EB' : '#F3F4F6')
				ctx.fillRect(tile.x, tile.y, tile.w, tile.h)

				if (imagePath) {
					this.drawCoverImage(ctx, imagePath, imageInfo, tile.x, tile.y, tile.w, tile.h, index)
				}

				if (tile.withStar && member) {
					const starW = tile.w * (this.layout === 'two' ? 0.96 : 0.96)
					const starH = tile.h * (this.layout === 'two' ? 0.76 : 0.72)
					const starX = tile.x + tile.w - starW + tile.w * 0.02
					const starY = tile.y + tile.h - starH + tile.h * 0.08
					ctx.drawImage(memberImage || member.asset, starX, starY, starW, starH)
					ctx.setFillStyle('rgba(0, 0, 0, 0.62)')
					ctx.fillRect(tile.x + 22, tile.y + tile.h - 70, tile.w - 44, 42)
					ctx.setFillStyle('#FFFFFF')
					ctx.setFontSize(this.layout === 'two' ? 24 : 20)
					ctx.fillText(member.name || this.selectedStar.name, tile.x + 40, tile.y + tile.h - 42)
				}

				ctx.restore()
				ctx.setStrokeStyle('#FFFFFF')
				ctx.setLineWidth(12)
				ctx.strokeRect(tile.x, tile.y, tile.w, tile.h)
			},
			drawCoverImage(ctx, imagePath, imageInfo, x, y, width, height, index) {
				if (!imageInfo || !imageInfo.width || !imageInfo.height) {
					const shift = index % 2 === 0 ? 0 : width * -0.08
					ctx.drawImage(imagePath, x + shift, y, width * 1.16, height)
					return
				}

				const sourceRatio = imageInfo.width / imageInfo.height
				const tileRatio = width / height
				let sourceX = 0
				let sourceY = 0
				let sourceW = imageInfo.width
				let sourceH = imageInfo.height

				if (sourceRatio > tileRatio) {
					sourceW = imageInfo.height * tileRatio
					sourceX = (imageInfo.width - sourceW) * (index % 2 === 0 ? 0.42 : 0.58)
				} else {
					sourceH = imageInfo.width / tileRatio
					sourceY = (imageInfo.height - sourceH) * (index % 2 === 0 ? 0.36 : 0.52)
				}

				ctx.drawImage(imagePath, sourceX, sourceY, sourceW, sourceH, x, y, width, height)
			},
			prepareImageInfo() {
				const uniqueImages = Array.from(new Set(this.userImages))
				const tasks = uniqueImages.map(path => new Promise(resolve => {
					uni.getImageInfo({
						src: path,
						success: info => resolve([path, info]),
						fail: () => resolve([path, null])
					})
				}))

				return Promise.all(tasks).then(entries => {
					return entries.reduce((map, [path, info]) => {
						map[path] = info
						return map
					}, {})
				})
			},
			getDrawableImage(path) {
				return new Promise(resolve => {
					uni.getImageInfo({
						src: path,
						success: info => resolve(info.path || path),
						fail: () => resolve(path)
					})
				})
			},
			async prepareMemberImages() {
				const members = this.selectedStar.members && this.selectedStar.members.length
					? this.selectedStar.members
					: [{ asset: this.selectedStar.asset }]
				const entries = await Promise.all(members.map(async member => {
					const path = await this.getDrawableImage(member.asset)
					return [member.asset, path]
				}))
				return entries.reduce((map, [asset, path]) => {
					map[asset] = path
					return map
				}, {})
			},
			getCanvasTiles() {
				if (this.layout === 'two') {
					return [
						{ x: 44, y: 44, w: 484, h: 888, withStar: false },
						{ x: 552, y: 44, w: 484, h: 888, withStar: true }
					]
				}

				return [
					{ x: 44, y: 44, w: 484, h: 420, withStar: false },
					{ x: 552, y: 44, w: 484, h: 420, withStar: true },
					{ x: 44, y: 488, w: 484, h: 420, withStar: false },
					{ x: 552, y: 488, w: 484, h: 420, withStar: true }
				]
			},
			canvasToImage() {
				return new Promise((resolve, reject) => {
					uni.canvasToTempFilePath({
						canvasId: 'frameCanvas',
						width: CANVAS_SIZE,
						height: CANVAS_SIZE,
						destWidth: CANVAS_SIZE,
						destHeight: CANVAS_SIZE,
						fileType: 'png',
						success: res => resolve(res.tempFilePath),
						fail: reject
					}, this)
				})
			},
			saveImage(tempFilePath) {
				return new Promise((resolve, reject) => {
					uni.saveImageToPhotosAlbum({
						filePath: tempFilePath,
						success: resolve,
						fail: error => reject({ ...error, tempFilePath })
					})
				})
			}
		}
	}
</script>

<style lang="scss" scoped>
	.frame-page {
		padding: calc(var(--status-bar-height) + 20rpx) 24rpx 178rpx;
		background: #F4F5F7;
	}

	.maker-top {
		display: grid;
		grid-template-columns: 72rpx 1fr 96rpx;
		align-items: center;
		gap: 18rpx;
		margin-bottom: 22rpx;
	}

	.top-copy {
		min-width: 0;
		display: flex;
		flex-direction: column;
		gap: 6rpx;
	}

	.top-title {
		color: #050505;
		font-size: 36rpx;
		font-weight: 900;
		line-height: 1.1;
	}

	.top-sub {
		color: #6B7280;
		font-size: 22rpx;
		font-weight: 800;
		line-height: 1.2;
		overflow: hidden;
		text-overflow: ellipsis;
		white-space: nowrap;
	}

	.top-pill {
		height: 56rpx;
		border-radius: 28rpx;
		background: #111827;
		color: #FFFFFF;
		display: flex;
		align-items: center;
		justify-content: center;
		font-size: 22rpx;
		font-weight: 900;
	}

	.preview-shell {
		padding: 22rpx;
		border-radius: 28rpx;
		background: #111827;
		box-shadow: 0 24rpx 46rpx rgba(17, 24, 39, 0.18);
	}

	.preview-stage {
		position: relative;
		display: grid;
		gap: 12rpx;
		aspect-ratio: 1;
		padding: 12rpx;
		border-radius: 18rpx;
		background: #FFFFFF;
		overflow: hidden;
	}

	.layout-two {
		grid-template-columns: repeat(2, 1fr);
	}

	.layout-four {
		grid-template-columns: repeat(2, 1fr);
		grid-template-rows: repeat(2, 1fr);
	}

	.preview-tile {
		position: relative;
		min-width: 0;
		min-height: 0;
		border-radius: 8px;
		overflow: hidden;
		background: #E5E7EB;
	}

	.user-photo,
	.star-cutout {
		position: absolute;
		inset: 0;
		width: 100%;
		height: 100%;
	}

	.star-cutout {
		inset: auto -10% 0 auto;
		width: 82%;
		height: 92%;
		filter: drop-shadow(-16rpx 18rpx 18rpx rgba(0, 0, 0, 0.24));
	}

	.layout-four .star-cutout {
		width: 74%;
		height: 88%;
	}

	.group-cutout,
	.layout-four .group-cutout {
		inset: auto -8% 4% auto;
		width: 112%;
		height: 74%;
	}

	.empty-photo {
		height: 100%;
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
		gap: 12rpx;
		color: #6B7280;
		font-size: 24rpx;
		font-weight: 900;
		background: linear-gradient(135deg, #F3F4F6, #E5E7EB);
	}

	.empty-photo text:first-child {
		width: 72rpx;
		height: 72rpx;
		border-radius: 50%;
		background: #FFFFFF;
		color: #111827;
		display: flex;
		align-items: center;
		justify-content: center;
		font-size: 42rpx;
		box-shadow: 0 8rpx 18rpx rgba(17, 24, 39, 0.08);
	}

	.name-strip {
		position: absolute;
		left: 16rpx;
		right: 16rpx;
		bottom: 16rpx;
		min-height: 56rpx;
		padding: 8rpx 14rpx;
		border-radius: 8px;
		background: rgba(0, 0, 0, 0.62);
		backdrop-filter: blur(10rpx);
		display: flex;
		align-items: center;
		justify-content: space-between;
		gap: 10rpx;
	}

	.name-strip text:first-child {
		color: #FFFFFF;
		font-size: 22rpx;
		font-weight: 900;
		overflow: hidden;
		text-overflow: ellipsis;
		white-space: nowrap;
	}

	.name-strip text:last-child {
		color: rgba(255, 255, 255, 0.74);
		font-size: 18rpx;
		font-weight: 800;
		flex: 0 0 auto;
	}

	.control-card,
	.star-section {
		margin-top: 22rpx;
		padding: 24rpx;
		border-radius: 24rpx;
		box-shadow: 0 18rpx 34rpx rgba(17, 24, 39, 0.05);
	}

	.section-line {
		display: flex;
		align-items: center;
		justify-content: space-between;
		gap: 18rpx;
		margin-bottom: 20rpx;
	}

	.section-line.compact {
		margin-bottom: 14rpx;
	}

	.control-title {
		color: #111827;
		font-size: 30rpx;
		font-weight: 900;
	}

	.layout-switch {
		padding: 6rpx;
		border-radius: 34rpx;
		background: #F1F2F5;
		display: grid;
		grid-template-columns: repeat(2, 120rpx);
		gap: 4rpx;
	}

	.layout-switch text {
		height: 56rpx;
		border-radius: 28rpx;
		color: #6B7280;
		font-size: 24rpx;
		font-weight: 900;
		display: flex;
		align-items: center;
		justify-content: center;
	}

	.layout-switch .active {
		background: #111827;
		color: #FFFFFF;
	}

	.mini-btn {
		height: 58rpx;
		padding: 0 22rpx;
		border-radius: 29rpx;
		background: #F3E8FF;
		color: #7C3AED;
		font-size: 24rpx;
		font-weight: 900;
	}

	.thumb-grid {
		display: grid;
		grid-template-columns: repeat(4, 1fr);
		gap: 12rpx;
	}

	.photo-thumb {
		position: relative;
		aspect-ratio: 1;
		border-radius: 14rpx;
		overflow: hidden;
		background: #F1F2F5;
		display: flex;
		align-items: center;
		justify-content: center;
		color: #111827;
		font-size: 36rpx;
		font-weight: 900;
	}

	.photo-thumb image {
		width: 100%;
		height: 100%;
	}

	.photo-thumb text {
		position: absolute;
		right: 6rpx;
		top: 6rpx;
		width: 34rpx;
		height: 34rpx;
		border-radius: 50%;
		background: rgba(0, 0, 0, 0.68);
		color: #FFFFFF;
		display: flex;
		align-items: center;
		justify-content: center;
		font-size: 24rpx;
		line-height: 1;
	}

	.photo-thumb.add {
		border: 2rpx dashed #CBD5E1;
		background: #FFFFFF;
	}

	.star-scroll {
		white-space: nowrap;
	}

	.star-row {
		display: flex;
		gap: 16rpx;
		padding-bottom: 2rpx;
	}

	.star-chip {
		width: 154rpx;
		flex: 0 0 auto;
		padding: 12rpx;
		border-radius: 18rpx;
		background: #F8FAFC;
		border: 2rpx solid transparent;
	}

	.star-chip.active {
		border-color: var(--star-accent);
		background: #FFFFFF;
		box-shadow: 0 10rpx 24rpx rgba(17, 24, 39, 0.08);
	}

	.star-avatar {
		position: relative;
		width: 130rpx;
		height: 130rpx;
		border-radius: 16rpx;
		overflow: hidden;
	}

	.star-avatar image {
		position: absolute;
		left: 8rpx;
		right: 8rpx;
		bottom: 0;
		width: 114rpx;
		height: 126rpx;
	}

	.star-name,
	.star-group {
		display: block;
		width: 100%;
		overflow: hidden;
		text-overflow: ellipsis;
		white-space: nowrap;
		text-align: center;
	}

	.star-name {
		margin-top: 10rpx;
		color: #111827;
		font-size: 22rpx;
		font-weight: 900;
	}

	.star-group {
		margin-top: 4rpx;
		color: #6B7280;
		font-size: 18rpx;
		font-weight: 800;
	}

	.bottom-actions {
		position: fixed;
		left: 0;
		right: 0;
		bottom: 0;
		z-index: 10;
		padding: 18rpx 24rpx calc(18rpx + env(safe-area-inset-bottom));
		background: rgba(247, 248, 250, 0.94);
		backdrop-filter: blur(16rpx);
		display: grid;
		grid-template-columns: 190rpx 1fr;
		gap: 16rpx;
	}

	.secondary-btn,
	.save-btn {
		height: 92rpx;
		border-radius: 8px;
		font-size: 30rpx;
		font-weight: 900;
	}

	.secondary-btn {
		background: #FFFFFF;
		color: #111827;
		box-shadow: 0 8rpx 24rpx rgba(17, 24, 39, 0.06);
	}

	.save-btn {
		background: #111827;
		color: #FFFFFF;
	}

	.save-btn.disabled {
		background: #D1D5DB;
	}

	.export-canvas {
		position: fixed;
		left: -2000px;
		top: -2000px;
		width: 1080px;
		height: 1080px;
	}
</style>
