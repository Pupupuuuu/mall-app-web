<template>
	<view class="container">
		<!-- 分类标签 -->
		<view class="category-tabs">
			<view v-for="(tab, index) in categoryTabs" :key="index" 
				  class="tab-item" 
				  :class="{active: currentTab === index}"
				  @click="switchTab(index)">
				<text>{{tab}}</text>
			</view>
		</view>
		
		<!-- 子榜单标签 -->
		<view class="sub-ranking-tabs">
			<scroll-view class="tabs-scroll" scroll-x :show-scrollbar="false">
				<view class="tabs-wrapper">
					<view v-for="(tab, index) in subRankingTabs" :key="index" 
						  class="sub-tab-item" 
						  :class="{active: currentSubTab === index}"
						  @click="switchSubTab(index)">
						<text>{{tab}}</text>
					</view>
				</view>
			</scroll-view>
		</view>
		
		<!-- 筛选区域 -->
		<view class="filter-section">
			<view class="filter-item">
				<text>商品类目</text>
				<text class="icon">▼</text>
			</view>
			<view class="filter-item">
				<text>价格</text>
				<text class="icon">▼</text>
			</view>
			<view class="filter-item">
				<text>保证率</text>
				<text class="icon">▼</text>
			</view>
			<view class="filter-item">
				<text>带货方式</text>
				<text class="icon">▼</text>
			</view>
		</view>
		
		<!-- 商品排行榜 -->
		<view class="ranking-list">
			<view v-for="(item, index) in rankingProducts" :key="index" class="ranking-item" @click="navToDetail(item)">
				<view class="rank-badge" :class="getRankClass(index + 1)">
					<text class="rank-number">{{index + 1}}</text>
				</view>
				<image :src="item.pic" class="product-image"></image>
				<view class="product-info">
					<text class="product-title">{{item.name}}</text>
					<text class="product-price">¥{{item.price}}</text>
					<text class="product-commission">佣金{{item.commission}}%</text>
					<text class="product-sales">近3日销量 {{item.sales}}W-{{item.salesMax}}W</text>
					<view class="product-tags">
						<text class="tag">推荐理由：{{item.reason}}</text>
						<text class="tag">推荐方式：{{item.method}}</text>
						<text class="tag">推荐平台：{{item.platform}}</text>
					</view>
				</view>
				<view class="trend-indicator">
					<text class="trend-icon">📈</text>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
export default {
	data() {
		return {
			currentTab: 0,
			categoryTabs: ['全部', '视频', '图文', '直播'],
			currentSubTab: 0,
			subRankingTabs: ['新兴榜', '潜力榜', '热销榜', '好货榜', '历史榜'],
			rankingProducts: [
				{
					id: 1,
					name: '【张杰同款】春羽羽绒服清洁剂中...',
					price: '9.9',
					commission: '27',
					sales: '2.76',
					salesMax: '2.76',
					reason: '潜力商品，自带流量，三坑热品',
					method: '直播/短视频',
					platform: '抖音/快手',
					pic: 'https://via.placeholder.com/160x160/ff6b6b/ffffff?text=爆品1'
				},
				{
					id: 2,
					name: '【网红推荐】时尚保温杯套装...',
					price: '15.8',
					commission: '35',
					sales: '5.2',
					salesMax: '8.1',
					reason: '网红爆款，复购率高',
					method: '短视频/图文',
					platform: '小红书/抖音',
					pic: 'https://via.placeholder.com/160x160/4ecdc4/ffffff?text=爆品2'
				},
				{
					id: 3,
					name: '【限时特惠】智能手机支架...',
					price: '29.9',
					commission: '42',
					sales: '3.8',
					salesMax: '6.5',
					reason: '实用性强，转化率高',
					method: '直播带货',
					platform: '快手/淘宝',
					pic: 'https://via.placeholder.com/160x160/45b7d1/ffffff?text=爆品3'
				},
				{
					id: 4,
					name: '【美妆必备】化妆刷套装...',
					price: '39.9',
					commission: '28',
					sales: '4.2',
					salesMax: '7.3',
					reason: '美妆热品，女性喜爱',
					method: '图文种草',
					platform: '小红书',
					pic: 'https://via.placeholder.com/160x160/f093fb/ffffff?text=爆品4'
				},
				{
					id: 5,
					name: '【居家神器】多功能收纳盒...',
					price: '19.9',
					commission: '31',
					sales: '6.1',
					salesMax: '9.8',
					reason: '居家必备，实用性强',
					method: '直播/短视频',
					platform: '抖音/快手',
					pic: 'https://via.placeholder.com/160x160/43e97b/ffffff?text=爆品5'
				}
			]
		}
	},
	methods: {
		switchTab(index) {
			this.currentTab = index;
			// 根据不同标签加载不同数据
			this.loadRankingByCategory();
		},
		switchSubTab(index) {
			this.currentSubTab = index;
			// 根据不同子榜单加载数据
			this.loadRankingBySubCategory();
		},
		loadRankingByCategory() {
			const category = this.categoryTabs[this.currentTab];
			console.log('切换到分类:', category);
			
			// 这里可以根据不同分类加载不同的排行榜数据
			uni.showToast({
				title: `${category}排行榜加载中`,
				icon: 'none'
			});
		},
		loadRankingBySubCategory() {
			const subCategory = this.subRankingTabs[this.currentSubTab];
			console.log('切换到子榜单:', subCategory);
			
			// 显示当前选中的子榜单，数据保持不变（使用虚假数据）
			uni.showToast({
				title: `${subCategory}数据加载中`,
				icon: 'none'
			});
		},
		getRankClass(rank) {
			if (rank <= 3) {
				return `rank-${rank}`;
			}
			return 'rank-normal';
		},
		navToDetail(item) {
			uni.navigateTo({
				url: `/pages/product/product?id=${item.id}`
			});
		}
	}
}
</script>

<style lang="scss" scoped>
.container {
	background: #f5f5f5;
	min-height: 100vh;
}

.category-tabs {
	display: flex;
	background: #fff;
	padding: 0 30upx;
	
	.tab-item {
		flex: 1;
		text-align: center;
		padding: 30upx 0;
		font-size: 28upx;
		color: #666;
		position: relative;
		
		&.active {
			color: #ff6b6b;
			font-weight: bold;
			
			&::after {
				content: '';
				position: absolute;
				bottom: 0;
				left: 50%;
				transform: translateX(-50%);
				width: 60upx;
				height: 4upx;
				background: #ff6b6b;
				border-radius: 2upx;
			}
		}
	}
}

.sub-ranking-tabs {
	background: #fff;
	border-top: 1px solid #f0f0f0;
	
	.tabs-scroll {
		white-space: nowrap;
		
		/* 隐藏滚动条 */
		::-webkit-scrollbar {
			display: none;
		}
		
		.tabs-wrapper {
			display: flex;
			padding: 0 30upx;
			
			.sub-tab-item {
				flex-shrink: 0;
				flex: 1;
				text-align: center;
				padding: 25upx 0;
				font-size: 28upx;
				color: #666;
				position: relative;
				min-width: 120upx;
				
				&.active {
					color: #ff6b6b;
					font-weight: bold;
					
					&::after {
						content: '';
						position: absolute;
						bottom: 0;
						left: 50%;
						transform: translateX(-50%);
						width: 60upx;
						height: 4upx;
						background: #ff6b6b;
						border-radius: 2upx;
					}
				}
			}
		}
	}
}

.filter-section {
	display: flex;
	background: #fff;
	padding: 20upx 30upx;
	border-top: 1px solid #f0f0f0;
	
	.filter-item {
		flex: 1;
		display: flex;
		align-items: center;
		justify-content: center;
		font-size: 28upx;
		color: #333;
		
		.icon {
			margin-left: 10upx;
			font-size: 20upx;
		}
	}
}

.ranking-list {
	padding: 20upx 30upx;
}

.ranking-item {
	display: flex;
	background: #fff;
	border-radius: 16upx;
	padding: 30upx;
	margin-bottom: 20upx;
	position: relative;
	box-shadow: 0 2upx 12upx rgba(0, 0, 0, 0.1);
	
	.rank-badge {
		position: absolute;
		top: -10upx;
		left: -10upx;
		width: 60upx;
		height: 60upx;
		border-radius: 50%;
		display: flex;
		align-items: center;
		justify-content: center;
		z-index: 2;
		box-shadow: 0 4upx 12upx rgba(0, 0, 0, 0.2);
		
		.rank-number {
			font-size: 24upx;
			font-weight: bold;
			color: #fff;
		}
		
		&.rank-1 {
			background: linear-gradient(135deg, #ffd700, #ffed4e);
			box-shadow: 0 4upx 12upx rgba(255, 215, 0, 0.4);
		}
		
		&.rank-2 {
			background: linear-gradient(135deg, #c0c0c0, #e8e8e8);
			box-shadow: 0 4upx 12upx rgba(192, 192, 192, 0.4);
		}
		
		&.rank-3 {
			background: linear-gradient(135deg, #cd7f32, #daa520);
			box-shadow: 0 4upx 12upx rgba(205, 127, 50, 0.4);
		}
		
		&.rank-normal {
			background: linear-gradient(135deg, #ff6b6b, #ff8e8e);
			box-shadow: 0 4upx 12upx rgba(255, 107, 107, 0.4);
		}
	}
	
	.product-image {
		width: 160upx;
		height: 160upx;
		border-radius: 12upx;
		margin-right: 20upx;
		margin-left: 20upx;
	}
	
	.product-info {
		flex: 1;
		
		.product-title {
			font-size: 28upx;
			color: #333;
			line-height: 1.4;
			margin-bottom: 10upx;
			display: block;
		}
		
		.product-price {
			font-size: 32upx;
			color: #ff6b6b;
			font-weight: bold;
			margin-bottom: 10upx;
			display: block;
		}
		
		.product-commission {
			font-size: 24upx;
			color: #666;
			margin-bottom: 10upx;
			display: block;
		}
		
		.product-sales {
			font-size: 24upx;
			color: #666;
			margin-bottom: 20upx;
			display: block;
		}
		
		.product-tags {
			.tag {
				display: block;
				font-size: 22upx;
				color: #999;
				line-height: 1.5;
			}
		}
	}
	
	.trend-indicator {
		position: absolute;
		top: 30upx;
		right: 30upx;
		
		.trend-icon {
			font-size: 40upx;
		}
	}
}
</style>