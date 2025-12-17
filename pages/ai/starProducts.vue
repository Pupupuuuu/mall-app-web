<template>
	<view class="container">
		<!-- 搜索区域 -->
		<view class="search-section">
			<view class="search-box">
				<input class="search-input" type="text" placeholder="请输入要查询的商品名称、商品ID" />
			</view>
			<view class="update-info">
				<text class="update-time">2025-12-10 16:28:09更新</text>
			</view>
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
		
		<!-- 商品列表 -->
		<view class="product-list">
			<view v-for="(item, index) in productList" :key="index" class="product-item" @click="navToDetail(item)">
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
				<view class="trend-icon">📈</view>
			</view>
		</view>
	</view>
</template>

<script>
export default {
	data() {
		return {
			categoryTitle: '星选专享', // 默认标题
			categoryType: 'star', // 默认类型
			productList: [
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
					pic: '/static/temp/product1.jpg'
				},
				{
					id: 2,
					name: '【张杰同款】春羽羽绒服清洁剂中...',
					price: '9.9',
					commission: '27',
					sales: '2.76',
					salesMax: '2.76',
					reason: '潜力商品，自带流量，三坑热品',
					method: '直播/短视频',
					platform: '抖音/快手',
					pic: '/static/temp/product2.jpg'
				},
				{
					id: 3,
					name: '【张杰同款】春羽羽绒服清洁剂中...',
					price: '9.9',
					commission: '27',
					sales: '2.76',
					salesMax: '2.76',
					reason: '潜力商品，自带流量，三坑热品',
					method: '直播/短视频',
					platform: '抖音/快手',
					pic: '/static/temp/product3.jpg'
				},
				{
					id: 4,
					name: '【张杰同款】春羽羽绒服清洁剂中...',
					price: '9.9',
					commission: '27',
					sales: '2.76',
					salesMax: '2.76',
					reason: '潜力商品，自带流量，三坑热品',
					method: '直播/短视频',
					platform: '抖音/快手',
					pic: '/static/temp/product4.jpg'
				}
			]
		}
	},
	onLoad(options) {
		// 接收传递的参数
		if (options.title) {
			this.categoryTitle = decodeURIComponent(options.title);
		}
		if (options.type) {
			this.categoryType = options.type;
		}
		
		// 根据类型加载不同的商品数据
		this.loadProductsByType();
		
		// 动态设置页面标题
		uni.setNavigationBarTitle({
			title: this.categoryTitle
		});
	},
	methods: {
		loadProductsByType() {
			// 根据不同类型加载不同的商品数据
			console.log('加载商品类型:', this.categoryType);
			
			// 根据类型生成不同的商品数据
			const baseProduct = {
				price: '9.9',
				commission: '27',
				sales: '2.76',
				salesMax: '2.76',
				method: '直播/短视频',
				platform: '抖音/快手'
			};
			
			switch(this.categoryType) {
				case 'winter':
					this.productList = [
						{
							...baseProduct,
							id: 1,
							name: '【冬季专款】保暖羽绒服清洁剂...',
							reason: '冬季热销，保暖必备',
							pic: 'https://via.placeholder.com/160x160/4a90e2/ffffff?text=冬季'
						},
						{
							...baseProduct,
							id: 2,
							name: '【冬日暖品】加厚毛毯套装...',
							reason: '冬季爆款，温暖舒适',
							pic: 'https://via.placeholder.com/160x160/4a90e2/ffffff?text=暖品'
						}
					];
					break;
				case 'season':
					this.productList = [
						{
							...baseProduct,
							id: 3,
							name: '【应季热销】时尚外套新款...',
							reason: '应季推荐，时尚百搭',
							pic: 'https://via.placeholder.com/160x160/7ed321/ffffff?text=应季'
						},
						{
							...baseProduct,
							id: 4,
							name: '【当季爆品】潮流运动鞋...',
							reason: '当季热门，运动时尚',
							pic: 'https://via.placeholder.com/160x160/7ed321/ffffff?text=当季'
						}
					];
					break;
				case 'sale':
					this.productList = [
						{
							...baseProduct,
							id: 5,
							name: '【双十二特惠】限时抢购套装...',
							reason: '双十二爆款，限时特惠',
							pic: 'https://via.placeholder.com/160x160/f5a623/ffffff?text=特惠'
						},
						{
							...baseProduct,
							id: 6,
							name: '【年终大促】超值组合装...',
							reason: '年终促销，超值优惠',
							pic: 'https://via.placeholder.com/160x160/f5a623/ffffff?text=大促'
						}
					];
					break;
				case 'video':
					this.productList = [
						{
							...baseProduct,
							id: 7,
							name: '【视频爆款】网红推荐好物...',
							reason: '视频热推，网红同款',
							pic: 'https://via.placeholder.com/160x160/d0021b/ffffff?text=视频'
						},
						{
							...baseProduct,
							id: 8,
							name: '【短视频热门】创意生活用品...',
							reason: '短视频爆红，创意实用',
							pic: 'https://via.placeholder.com/160x160/d0021b/ffffff?text=热门'
						}
					];
					break;
				case 'star':
					this.productList = [
						{
							...baseProduct,
							id: 9,
							name: '【星选专享】精选优质好物...',
							reason: '星选推荐，品质保证',
							pic: 'https://via.placeholder.com/160x160/667eea/ffffff?text=星选'
						},
						{
							...baseProduct,
							id: 10,
							name: '【专享特惠】限量精品套装...',
							reason: '专享优惠，限量发售',
							pic: 'https://via.placeholder.com/160x160/764ba2/ffffff?text=专享'
						},
						{
							...baseProduct,
							id: 11,
							name: '【星级推荐】高端生活用品...',
							reason: '星级品质，生活必备',
							pic: 'https://via.placeholder.com/160x160/43e97b/ffffff?text=星级'
						}
					];
					break;
				default:
					// 保持原有的默认数据
					break;
			}
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

.search-section {
	background: #fff;
	padding: 20upx 30upx;
	
	.search-box {
		background: #f8f8f8;
		border-radius: 50upx;
		padding: 20upx 30upx;
		margin-bottom: 15upx;
		
		.search-input {
			font-size: 28upx;
			color: #666;
		}
	}
	
	.update-info {
		text-align: center;
		
		.update-time {
			font-size: 24upx;
			color: #999;
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

.product-list {
	padding: 20upx 30upx;
}

.product-item {
	display: flex;
	background: #fff;
	border-radius: 16upx;
	padding: 30upx;
	margin-bottom: 20upx;
	position: relative;
	box-shadow: 0 2upx 12upx rgba(0, 0, 0, 0.1);
	
	.product-image {
		width: 160upx;
		height: 160upx;
		border-radius: 12upx;
		margin-right: 20upx;
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
	
	.trend-icon {
		position: absolute;
		top: 30upx;
		right: 30upx;
		font-size: 40upx;
	}
}
</style>