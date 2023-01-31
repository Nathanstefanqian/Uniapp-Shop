<template>
	<view class="my-search-container" :style="{'background-color':bgcolor}">
		<view class="my-search-box" :style="{'border-radius':radius+'px'}" @click="searchBoxHandler">
			<uni-icons type="search" size="17"></uni-icons>
			<text class="placeholder">搜索</text>
		</view>
	</view>
</template>

<script>
	export default {
		name: 'my-search',
		props: {
			//背景颜色
			bgcolor: {
				type: String,
				default: '#C00000'
			},
			//圆角尺寸
			radius: {
				type: Number,
				default: 18
			}
		},
		data() {
			return { seconds: 3 }
		},
		methods: {
			searchBoxHandler() {
				//触发外界通过@click绑定的click事件处理函数
				this.$emit('click')
			},
			showTips(n) {
				// 调用 uni.showToast() 方法，展示提示消息
				uni.showToast({
					// 不展示任何图标
					icon: 'none',
					// 提示的消息
					title: '请登录后再结算！' + n + ' 秒后自动跳转到登录页',
					// 为页面添加透明遮罩，防止点击穿透
					mask: true,
					// 1.5 秒后自动消失
					duration: 1500
				})
			}
		}
	}
</script>

<style lang="scss">
	.my-search-container {
		height: 50px;
		padding: 0 10px;
		display: flex;
		align-items: center;
	}

	.my-search-box {
		height: 36px;
		background-color: #ffffff;
		width: 100%;
		display: flex;
		align-items: center;
		justify-content: center;

		.placeholder {
			font-size: 15px;
			margin-left: 5px;
		}
	}
</style>
