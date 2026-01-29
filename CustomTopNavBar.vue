<template>
	<view class="custom-top-navigation-bar">
		<!-- 背景层：纯视觉，fixed，不挡点击 -->
		<view class="bg-layer" :style="bgStyle">
			<image class="bg-img" :src="bg" mode="widthFix" />
		</view>

		<!-- 导航层：fixed -->
		<view class="nav-fixed" :style="navFixedStyle">
			<!-- 状态栏占位 -->
			<view class="top-status-bar" :style="{ height: statusBarHeight + 'px' }"></view>

			<!-- 标题栏 -->
			<view class="top-title-bar" :style="{ height: titleBarHeight + 'px' }">
				<!-- 左侧 -->
				<view class="left" @click="handleBack" v-if="showBack">
					<uni-icons class="back-icon" type="left" size="24" :color="iconColor"></uni-icons>
				</view>
				<view class="left" v-else></view>

				<!-- 中间标题 -->
				<view class="center">
					<text class="title-text" :style="{ color: titleColor }">{{ title }}</text>
				</view>

				<!-- 右侧占位，保证居中 -->
				<view class="right"></view>
			</view>
		</view>

		<!-- 占位：把页面内容顶下来 -->
		<view class="nav-placeholder" :style="{ height: navBarHeight + 'px' }"></view>
	</view>
</template>

<script setup>
	import {
		computed
	} from 'vue'

	/**
	 * 内置工具：系统信息（基于官方API获取状态栏高度）
	 */
	const WINDOW_INFO = (() => {
		try {
			return uni.getWindowInfo ? uni.getWindowInfo() : (uni.getSystemInfoSync ? uni.getSystemInfoSync() : {})
		} catch (e) {
			return {}
		}
	})()

	// 获取顶部状态栏高度
	const getStatusBarHeight = () => WINDOW_INFO.statusBarHeight || 15

	// 获取小程序胶囊按钮标题栏高度
	const getTitleBarHeight = () => {
		try {
			if (uni.getMenuButtonBoundingClientRect) {
				const {
					top,
					height
				} = uni.getMenuButtonBoundingClientRect()
				return height + (top - getStatusBarHeight()) * 2
			}
			return 40
		} catch (e) {
			return 40
		}
	}

	// 整体导航栏高度 =  状态栏高度 + 胶囊按钮标题栏高度
	const getNavBarHeight = () => getStatusBarHeight() + getTitleBarHeight()

	// 抖音小程序左侧LOGO遮挡适配
	const getLeftIconLeft = () => {
		// #ifdef MP-TOUTIAO
		try {
			const {
				leftIcon: {
					left,
					width
				}
			} = tt.getCustomButtonBoundingClientRect()
			return left + parseInt(width)
		} catch (e) {
			return 0
		}
		// #endif

		// #ifndef MP-TOUTIAO
		return 0
		// #endif
	}

	const props = defineProps({
		/** 标题 */
		title: {
			type: String,
			default: '自定义标题'
		},

		/** 是否显示返回 */
		showBack: {
			type: Boolean,
			default: true
		},

		/** 自定义返回逻辑 */
		back: {
			type: Function,
			default: null
		},

		/** 背景图 */
		bg: {
			type: String,
			default: 'https://xxx.com/your-bg.svg'
		},

		/** 颜色 */
		titleColor: {
			type: String,
			default: '#D1D1D3'
		},
		iconColor: {
			type: String,
			default: '#D1D1D3'
		},

		/** z-index 控制 */
		navZIndex: {
			type: Number,
			default: 10
		},
		bgZIndex: {
			type: Number,
			default: 0
		},

		/** 底部分割线 */
		showBorder: {
			type: Boolean,
			default: true
		},
		borderColor: {
			type: String,
			default: 'rgba(255,255,255,0.08)'
		},
		borderWidth: {
			type: [Number, String],
			default: 1
		}
	})

	const statusBarHeight = getStatusBarHeight()
	const titleBarHeight = getTitleBarHeight()
	const navBarHeight = computed(() => getNavBarHeight())

	/** 背景层样式 */
	const bgStyle = computed(() => ({
		zIndex: props.bgZIndex
	}))

	/** 导航层样式 */
	const navFixedStyle = computed(() => ({
		zIndex: props.navZIndex,
		borderBottom: props.showBorder ?
			`${typeof props.borderWidth === 'number' ? props.borderWidth + 'px' : props.borderWidth} solid ${props.borderColor}` :
			'none'
	}))

	const handleBack = () => {
		if (typeof props.back === 'function') return props.back()

		const pages = (typeof getCurrentPages === 'function' ? getCurrentPages() : []) || []
		if (pages.length > 1) {
			uni.navigateBack()
		} else {
			uni.switchTab?.({
				url: '/pages/index/index'
			})
		}
	}

	/**
	 * 可选：父组件想拿高度/抖音遮挡信息时，不用再引工具
	 * 用法：const navRef = ref(); navRef.value.navBarHeight
	 */
	defineExpose({
		statusBarHeight,
		titleBarHeight,
		navBarHeight,
		getLeftIconLeft
	})
</script>

<style scoped lang="scss">
	.custom-top-navigation-bar {
		width: 100%;
		position: relative;
	}

	/* 背景层：纯背景，不拦截点击 */
	.bg-layer {
		position: fixed;
		top: 0;
		left: 0;
		width: 100%;
		pointer-events: none;
	}

	.bg-img {
		width: 100%;
		display: block;
	}

	/* 导航固定层 */
	.nav-fixed {
		position: fixed;
		top: 0;
		left: 0;
		width: 100%;
	}

	/* 状态栏占位 */
	.top-status-bar {
		width: 100%;
	}

	/* 标题栏 */
	.top-title-bar {
		display: flex;
		align-items: center;
		padding: 0 24rpx;
		box-sizing: border-box;
	}

	/* 三段式，保证标题真居中 */
	.left,
	.right {
		width: 80rpx;
		display: flex;
		align-items: center;
	}

	.center {
		flex: 1;
		display: flex;
		align-items: center;
		justify-content: center;
	}

	.title-text {
		font-size: 32rpx;
		line-height: 1;
		padding-bottom: 4rpx;
	}

	.back-icon {
		transform: translateY(1rpx);
	}

	/* 页面占位 */
	.nav-placeholder {
		width: 100%;
	}
</style>