一个开箱即用的 uni-app 顶部导航组件：

内置状态栏/标题栏高度计算（适配胶囊按钮）

顶部背景图宽度 100% 等比自适应（mode="widthFix"）

导航 fixed，并自动生成占位，避免页面内容被遮挡

支持自定义标题、返回逻辑、z-index、分割线颜色/粗细

父组件可直接拿到导航高度（无需额外工具库）

把组件文件放到(或在uniapp插件市场下载导入)：
/components/CustomTopNavBar.vue

在页面中引入使用（Vue3 setup）：

<script setup>
import CustomTopNavBar from '@/components/CustomTopNavBar.vue'
</script>

2. 快速开始
2.1 最简单用法
<template>
  <CustomTopNavBar title="首页" />
  <view>页面内容...</view>
</template>

3. 常用用法示例
3.1 自定义背景图
<CustomTopNavBar
  title="首页"
  bg="https://your-cdn.com/top-bg.svg"
/>


说明：背景采用 image + mode="widthFix"

宽度 100%

高度按图片比例自动计算

不建议用作“参与布局的头图”，它是纯背景视觉层。

3.2 隐藏返回按钮
<CustomTopNavBar
  title="首页"
  :showBack="false"
/>

3.3 自定义返回逻辑
<script setup>
const goHome = () => {
  uni.reLaunch({ url: '/pages/index/index' })
}
</script>

<template>
  <CustomTopNavBar title="详情" :back="goHome" />
</template>


不传 back 时默认逻辑：

有上一页：uni.navigateBack()

无上一页：uni.switchTab('/pages/index/index')

3.4 自定义标题/图标颜色
<CustomTopNavBar
  title="设置"
  titleColor="#FFFFFF"
  iconColor="#FFFFFF"
/>

3.5 自定义层级（z-index）
<CustomTopNavBar
  :navZIndex="100"
  :bgZIndex="0"
/>


推荐：

背景：0

导航：10~100

弹窗：更高

3.6 自定义底部分割线（颜色 + 粗细）
<CustomTopNavBar
  :showBorder="true"
  borderColor="rgba(0,0,0,0.12)"
  :borderWidth="2"
/>


也可以传字符串：

<CustomTopNavBar
  borderWidth="1px"
  borderColor="#2C2C2E"
/>


关闭分割线：

<CustomTopNavBar :showBorder="false" />

4. Props 参数说明
参数	类型	默认值	说明
title	String	自定义标题	顶部标题
showBack	Boolean	true	是否显示返回按钮
back	Function	null	自定义返回逻辑（优先级最高）
bg	String	https://xxx.com/your-bg.svg
	顶部背景图
titleColor	String	#D1D1D3	标题颜色
iconColor	String	#D1D1D3	返回图标颜色
navZIndex	Number	10	导航层 z-index
bgZIndex	Number	0	背景层 z-index
showBorder	Boolean	true	是否显示底部分割线
borderColor	String	rgba(255,255,255,0.08)	分割线颜色
borderWidth	Number/String	1	分割线粗细，支持 2 或 1px
5. 获取导航高度（无需工具库）

组件通过 defineExpose 暴露高度信息，父组件可直接获取：

<script setup>
import { ref, onMounted } from 'vue'
import CustomTopNavBar from '@/components/CustomTopNavBar.vue'

const navRef = ref(null)

onMounted(() => {
  console.log('statusBarHeight(px):', navRef.value.statusBarHeight)
  console.log('titleBarHeight(px):', navRef.value.titleBarHeight)
  console.log('navBarHeight(px):', navRef.value.navBarHeight)
})
</script>

<template>
  <CustomTopNavBar ref="navRef" title="页面标题" />
</template>

暴露字段/方法
名称	类型	说明
statusBarHeight	Number	状态栏高度（px）
titleBarHeight	Number	标题栏高度（px）
navBarHeight	ComputedRef(Number)	导航总高度（px）
getLeftIconLeft	Function	抖音小程序左侧 LOGO 遮挡适配计算
6. 注意事项
6.1 背景不拦截点击

组件背景层默认 pointer-events: none，不会挡住页面操作，这是纯背景组件必须具备的行为。

6.2 这是“顶栏背景”，不是 Banner

背景图会固定在顶部，不参与布局高度计算（占位只来自导航栏本身），适合光效/渐变/装饰层。

7. License

MIT（可商用）
