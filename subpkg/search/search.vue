<template>
	<view>
		<view class="search-box">
			<uni-search-bar @input="input" :radius="100" cancelButton="none"></uni-search-bar>
		</view>
		<view class="sugg-list" v-if="searchResults.length !==0">
			<view class="sugg-item" v-for="(item, i) in searchResults" :key="i" @click="gotoDetail(item.goods_id)">
				<view class="goods-name">{{item.goods_name}}</view>
				<uni-icons type="arrowright" size="16"></uni-icons>
			</view>
		</view>
		<!-- 搜索历史 -->
		<view class="history-box" v-else>
			<!-- 标题区域 -->
			<view class="history-title">
				<text>搜索历史</text>
				<uni-icons type="trash" size="17" @click="cleanHistory"></uni-icons>
			</view>
			<!-- 列表区域 -->
			<view class="history-list">
				<uni-tag :text="item" v-for="(item, i) in historyList" :key="i" @click="gotoGoodsList(item)"></uni-tag>
			</view>
		</view>
	</view>

</template>

<script>
	export default {
		data() {
			return {
				//定时器id
				timer: null,
				kw: '',
				searchResults: [],
				historyList: []
			}
		},
		onLoad() {
			this.historyList = JSON.parse(uni.getStorageSync('kw') || '[]')
		},
		methods: {
			async getSearchList() {
				if (this.kw === '') {
					this.searchResults = []
					return
				}
				const { data: res } = await uni.$http.get('/api/public/v1/goods/qsearch', { query: this.kw })
				if (res.meta.status !== 200) return uni.$showMsg()
				this.searchResults = res.message
				this.saveSearchHistory()
			},
			input(e) {
				clearTimeout(this.timer)
				this.timer = setTimeout(() => {
					//如果500ms内，没有触发新的输入事件，则为搜索关键词赋值
					this.kw = e //这里直接用e,不能用e.value了
					this.getSearchList()
					console.log(this.kw)
				}, 500)
			},
			gotoDetail(goods_id) {
				uni.navigateTo({
					// 指定详情页面的 URL 地址，并传递 goods_id 参数
					url: '/subpkg/goods_detail/goods_detail?goods_id=' + goods_id
				})
			},
			saveSearchHistory() {
				this.historyList = [...new Set([this.kw, ...this.historyList])]
				uni.setStorageSync('kw', JSON.stringify(this.historyList))
			},
			cleanHistory() {
				this.historyList = []
				uni.setStorageSync('kw', '[]')
			},
			gotoGoodsList(kw) {
				uni.navigateTo({ url: '/subpkg/goods_list/goods_list?query=' + kw })
			}
		}
	}
</script>

<style lang="scss">
	.search-box {
		position: sticky;
		top: 0;
		z-index: 999;
	}

	.sugg-list {
		padding: 0 5px;

		.sugg-item {
			font-size: 12px;
			padding: 13px 0;
			border-bottom: 1px solid #efefef;
			display: flex;
			align-items: center;
			justify-content: space-between;

			.goods-name {
				// 文字不允许换行（单行文本）
				white-space: nowrap;
				// 溢出部分隐藏
				overflow: hidden;
				// 文本溢出后，使用 ... 代替
				text-overflow: ellipsis;
				margin-right: 3px;
			}
		}
	}

	.history-box {
		padding: 0 5px;

		.history-title {
			display: flex;
			justify-content: space-between;
			align-items: center;
			height: 40px;
			font-size: 13px;
			border-bottom: 1px solid #efefef;
		}

		.history-list {
			display: flex;
			flex-wrap: wrap;

			.uni-tag {
				margin-top: 5px;
				margin-right: 5px;
			}
		}
	}
</style>
