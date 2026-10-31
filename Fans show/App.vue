<script>
	export default {
		onLaunch() {
			console.log('Fans show Launch')
		}
	}
</script>

<style lang="scss">
	@import "./uni.scss";

	page {
		background: #F7F8FA;
		color: #111827;
		font-family: -apple-system, BlinkMacSystemFont, "Helvetica Neue", Helvetica, Arial, sans-serif;
	}

	view,
	text,
	input,
	textarea,
	button {
		box-sizing: border-box;
	}

	button {
		margin: 0;
		padding: 0;
		background: transparent;
		border: 0;
		line-height: 1;
	}

	button::after {
		border: 0;
	}

	.page {
		min-height: 100vh;
		padding: calc(var(--status-bar-height) + 20rpx) 32rpx 150rpx;
		background: #F7F8FA;
	}

	.page-title {
		font-size: 42rpx;
		font-weight: 800;
		color: #111827;
		letter-spacing: 0;
	}

	.top-bar {
		display: flex;
		align-items: center;
		justify-content: space-between;
		min-height: 72rpx;
		margin-bottom: 24rpx;
	}

	.icon-btn {
		width: 72rpx;
		height: 72rpx;
		border-radius: 8px;
		display: flex;
		align-items: center;
		justify-content: center;
		background: #FFFFFF;
		color: #111827;
		font-size: 36rpx;
		box-shadow: 0 8rpx 24rpx rgba(17, 24, 39, 0.05);
	}

	.card {
		background: #FFFFFF;
		border-radius: 8px;
		box-shadow: 0 10rpx 30rpx rgba(17, 24, 39, 0.05);
	}

	.pressable {
		transition: transform 0.16s ease, opacity 0.16s ease, background-color 0.16s ease;
	}

	.pressable:active {
		transform: scale(0.97);
		opacity: 0.82;
	}

	.primary-btn {
		height: 96rpx;
		padding: 0 32rpx;
		border-radius: 8px;
		background: #7C3AED;
		color: #FFFFFF;
		font-size: 30rpx;
		font-weight: 700;
		display: flex;
		align-items: center;
		justify-content: center;
	}

	.muted {
		color: #6B7280;
	}

	.section-head {
		display: flex;
		align-items: center;
		justify-content: space-between;
		margin: 32rpx 0 18rpx;
	}

	.section-title {
		font-size: 32rpx;
		font-weight: 800;
		color: #111827;
	}

	.section-more {
		font-size: 24rpx;
		color: #7C3AED;
	}

	.tag {
		padding: 10rpx 18rpx;
		border-radius: 8px;
		background: #F3E8FF;
		color: #7C3AED;
		font-size: 22rpx;
	}
</style>
