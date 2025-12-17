<template>
	<view class="container">
		<!-- 小程序头部兼容 -->
		<!-- #ifdef MP -->
		<view class="mp-search-box">
			<input class="ser-input" type="text" value="输入关键字搜索" disabled />
		</view>
		<!-- #endif -->

		<!-- 头部轮播 -->
		<view class="carousel-section">
			<!-- 标题栏和状态栏占位符 -->
			<view class="titleNview-placing"></view>
			<!-- 背景色区域 -->
			<view class="titleNview-background" :style="{backgroundColor:titleNViewBackground}"></view>
			<swiper class="carousel" circular @change="swiperChange">
				<swiper-item v-for="(item, index) in advertiseList" :key="index" class="carousel-item" @click="navToAdvertisePage(item)">
					<image :src="item.pic" />
				</swiper-item>
			</swiper>
			<!-- 自定义swiper指示器 -->
			<view class="swiper-dots">
				<text class="num">{{swiperCurrent+1}}</text>
				<text class="sign">/</text>
				<text class="num">{{swiperLength}}</text>
			</view>
		</view>
		<!-- AI功能导航区 - 按原型图8个功能 -->
		<view class="ai-nav-section">
			<view class="nav-grid">
				<view class="nav-item" @click="navToAIPage('hotRanking')">
					<view class="nav-icon hot">🔥</view>
					<text>爆品榜</text>
				</view>
				<view class="nav-item" @click="showDeveloping">
					<view class="nav-icon expert">👤</view>
					<text>达人榜</text>
				</view>
				<view class="nav-item" @click="showDeveloping">
					<view class="nav-icon live">📺</view>
					<text>直播榜</text>
				</view>
				<view class="nav-item" @click="showDeveloping">
					<view class="nav-icon video">🎬</view>
					<text>视频榜</text>
				</view>
				<view class="nav-item" @click="showDeveloping">
					<view class="nav-icon avatar">🤖</view>
					<text>数字分身</text>
				</view>
				<view class="nav-item" @click="showDeveloping">
					<view class="nav-icon ai-video">🎥</view>
					<text>AI视频</text>
				</view>
				<view class="nav-item" @click="showDeveloping">
					<view class="nav-icon ai-copy">✍️</view>
					<text>AI文案</text>
				</view>
				<view class="nav-item" @click="showDeveloping">
					<view class="nav-icon ai-tools">🛠️</view>
					<text>AI工具箱</text>
				</view>
			</view>
		</view>
		
		<!-- 星选专享区域 -->
		<view class="star-exclusive-section">
			<!-- 左侧星选专享按钮 -->
			<view class="star-button" @click="navToStarSelection">
				<text class="vertical-text">星选专享</text>
			</view>
			<!-- 右侧四宫格 -->
			<view class="star-grid">
				<view v-for="(item, index) in starProducts" :key="index" class="grid-item" @click="navToSeasonPage(item)">
					<view class="grid-tag" :class="item.tagClass">{{item.tag}}</view>
					<image :src="item.pic" class="grid-image"></image>
					<text class="grid-label">{{item.label}}</text>
				</view>
			</view>
		</view>
		
		<!-- 筛选区域 -->
		<view class="filter-section">
			<view class="filter-row">
				<view class="filter-item" @click="showFilterDropdown('platform')">
					<text class="filter-label">{{currentFilters.platform}}</text>
					<text class="filter-arrow">▼</text>
				</view>
				<view class="filter-item" @click="showFilterDropdown('sales')">
					<text class="filter-label">{{currentFilters.sales}}</text>
					<text class="filter-arrow">▼</text>
				</view>
				<view class="filter-item" @click="showFilterDropdown('commission')">
					<text class="filter-label">{{currentFilters.commission}}</text>
					<text class="filter-arrow">▼</text>
				</view>
				<view class="filter-item" @click="showFilterDropdown('sort')">
					<text class="filter-label">{{currentFilters.sort}}</text>
					<text class="filter-arrow">▼</text>
				</view>
			</view>
		</view>
		
		<!-- 下拉选择弹窗 -->
		<view class="dropdown-mask" :class="{show: showDropdown}" @click="hideDropdown">
			<view class="dropdown-content" @click.stop>
				<view class="dropdown-title">{{dropdownTitle}}</view>
				<view class="dropdown-list">
					<view v-for="(option, index) in currentOptions" :key="index" 
						  class="dropdown-option" 
						  :class="{active: option.selected}"
						  @click="selectOption(option)">
						<text>{{option.label}}</text>
						<text v-if="option.selected" class="check-icon">✓</text>
					</view>
				</view>
			</view>
		</view>

		<!-- 品牌制造商直供 -->
<!--		<view class="f-header m-t" @click="navToRecommendBrandPage()">-->
<!--			<image src="/static/icon_home_brand.png"></image>-->
<!--			<view class="tit-box">-->
<!--				<text class="tit">品牌制造商直供</text>-->
<!--				<text class="tit2">工厂直达消费者，剔除品牌溢价</text>-->
<!--			</view>-->
<!--			<text class="yticon icon-you"></text>-->
<!--		</view>-->
<!--		<view class="guess-section">-->
<!--			<view v-for="(item, index) in brandList" :key="index" class="guess-item" @click="navToBrandDetailPage(item)">-->
<!--				<view class="image-wrapper-brand">-->
<!--					<image :src="item.logo" mode="aspectFit"></image>-->
<!--				</view>-->
<!--				<text class="title clamp">{{item.name}}</text>-->
<!--				<text class="title2">商品数量：{{item.productCount}}</text>-->
<!--			</view>-->
<!--		</view>-->

		<!-- 秒杀专区 -->
<!--		<view class="f-header m-t" v-if="homeFlashPromotion!==null">-->
<!--			<image src="/static/icon_flash_promotion.png"></image>-->
<!--			<view class="tit-box">-->
<!--				<text class="tit">秒杀专区</text>-->
<!--				<text class="tit2">下一场 {{homeFlashPromotion.nextStartTime | formatTime}} 开始</text>-->
<!--			</view>-->
<!--			<view class="tit-box">-->
<!--				<text class="tit2" style="text-align: right;">本场结束剩余：</text>-->
<!--				<view style="text-align: right;">-->
<!--					<text class="hour timer">{{cutDownTime.endHour}}</text>-->
<!--					<text>:</text>-->
<!--					<text class="minute timer">{{cutDownTime.endMinute}}</text>-->
<!--					<text>:</text>-->
<!--					<text class="second timer">{{cutDownTime.endSecond}}</text>-->
<!--				</view>-->
<!--			</view>-->
<!--			<text class="yticon icon-you" v-show="false"></text>-->
<!--		</view>-->
<!--		<view class="guess-section">-->
<!--			<view v-for="(item, index) in homeFlashPromotion.productList" :key="index" class="guess-item" @click="navToDetailPage(item)">-->
<!--				<view class="image-wrapper">-->
<!--					<image :src="item.pic" mode="aspectFill"></image>-->
<!--				</view>-->
<!--				<text class="title clamp">{{item.name}}</text>-->
<!--				<text class="title2 clamp">{{item.subTitle}}</text>-->
<!--				<text class="price">￥{{item.price}}</text>-->
<!--			</view>-->
<!--		</view>-->

		<!-- 新鲜好物 -->
<!--		<view class="f-header m-t" @click="navToNewProudctListPage()">-->
<!--			<image src="/static/icon_new_product.png"></image>-->
<!--			<view class="tit-box">-->
<!--				<text class="tit">新鲜好物</text>-->
<!--				<text class="tit2">为你寻觅世间好物</text>-->
<!--			</view>-->
<!--			<text class="yticon icon-you"></text>-->
<!--		</view>-->
<!--		<view class="seckill-section">-->
<!--			<scroll-view class="floor-list" scroll-x>-->
<!--				<view class="scoll-wrapper">-->
<!--					<view v-for="(item, index) in newProductList" :key="index" class="floor-item" @click="navToDetailPage(item)">-->
<!--						<image :src="item.pic" mode="aspectFill"></image>-->
<!--						<text class="title clamp">{{item.name}}</text>-->
<!--						<text class="title2 clamp">{{item.subTitle}}</text>-->
<!--						<text class="price">￥{{item.price}}</text>-->
<!--					</view>-->
<!--				</view>-->
<!--			</scroll-view>-->
<!--		</view>-->

		<!-- 人气推荐楼层 -->
<!--		<view class="f-header m-t" @click="navToHotProudctListPage()">-->
<!--			<image src="/static/icon_hot_product.png"></image>-->
<!--			<view class="tit-box">-->
<!--				<text class="tit">人气推荐</text>-->
<!--				<text class="tit2">大家都赞不绝口的</text>-->
<!--			</view>-->
<!--			<text class="yticon icon-you"></text>-->
<!--		</view>-->
<!--		<view class="hot-section">-->
<!--			<view v-for="(item, index) in hotProductList" :key="index" class="guess-item" @click="navToDetailPage(item)">-->
<!--				<view class="image-wrapper">-->
<!--					<image :src="item.pic" mode="aspectFill"></image>-->
<!--				</view>-->
<!--				<view class="txt">-->
<!--					<text class="title clamp">{{item.name}}</text>-->
<!--					<text class="title2">{{item.subTitle}}</text>-->
<!--					<text class="price">￥{{item.price}}</text>-->
<!--				</view>-->
<!--			</view>-->
<!--		</view>-->

		<!-- 猜你喜欢-->
<!--		<view class="f-header m-t">-->
<!--			<image src="/static/icon_recommend_product.png"></image>-->
<!--			<view class="tit-box">-->
<!--				<text class="tit">猜你喜欢</text>-->
<!--				<text class="tit2">你喜欢的都在这里了</text>-->
<!--			</view>-->
<!--			<text class="yticon icon-you" v-show="false"></text>-->
<!--		</view>-->

		<view class="guess-section">
			<view v-for="(item, index) in recommendProductList" :key="index" class="guess-item" @click="navToDetailPage(item)">
				<view class="image-wrapper">
					<image :src="item.pic" mode="aspectFill"></image>
				</view>
				<text class="title clamp">{{item.name}}</text>
				<text class="title2 clamp">{{item.subTitle}}</text>
				<text class="price">￥{{item.price}}</text>
			</view>
		</view>
		<uni-load-more :status="loadingType"></uni-load-more>
	</view>
</template>

<script>
	import {
		fetchContent,
		fetchRecommendProductList
	} from '@/api/home.js';
	import {
		formatDate
	} from '@/utils/date';
	import uniLoadMore from '@/components/uni-load-more/uni-load-more.vue';
	export default {
		components: {
			uniLoadMore	
		},
		data() {
			return {
				titleNViewBackground: '',
				titleNViewBackgroundList: ['rgb(203, 87, 60)', 'rgb(205, 215, 218)'],
				swiperCurrent: 0,
				swiperLength: 0,
				carouselList: [],
				goodsList: [],
				advertiseList: [],
				brandList: [],
				homeFlashPromotion: [],
				newProductList: [],
				hotProductList: [],
				recommendProductList: [],
				recommendParams: {
					pageNum: 1,
					pageSize: 4
				},
				loadingType:'more',
				// 星选专享四宫格数据
				starProducts: [
					{
						id: 1,
						tag: '冬季爆款',
						tagClass: 'winter-tag',
						pic: 'https://via.placeholder.com/300x150/4a90e2/ffffff?text=冬季爆款',
						label: '冬日新款',
						type: 'winter'
					},
					{
						id: 2,
						tag: '应季爆款',
						tagClass: 'season-tag',
						pic: 'https://via.placeholder.com/300x150/7ed321/ffffff?text=应季爆款',
						label: '冬日新款',
						type: 'season'
					},
					{
						id: 3,
						tag: '双十二爆款',
						tagClass: 'sale-tag',
						pic: 'https://via.placeholder.com/300x150/f5a623/ffffff?text=双十二',
						label: '冬日新款',
						type: 'sale'
					},
					{
						id: 4,
						tag: '视频爆款榜',
						tagClass: 'video-tag',
						pic: 'https://via.placeholder.com/300x150/d0021b/ffffff?text=视频榜',
						label: '冬日新款',
						type: 'video'
					}
				],
				// 筛选相关数据
				showDropdown: false,
				currentFilterType: '',
				dropdownTitle: '',
				currentOptions: [],
				currentFilters: {
					platform: '平台选择',
					sales: '昨日销量',
					commission: '佣金比例',
					sort: '最新上架'
				},
				// 筛选选项数据
				filterOptions: {
					platform: [
						{label: '平台选择', value: 'all', selected: true},
						{label: '抖音', value: 'douyin', selected: false},
						{label: '快手', value: 'kuaishou', selected: false},
						{label: '小红书', value: 'xiaohongshu', selected: false},
						{label: '淘宝直播', value: 'taobao', selected: false},
						{label: '京东直播', value: 'jingdong', selected: false}
					],
					sales: [
						{label: '昨日销量', value: 'all', selected: true},
						{label: '1000+', value: '1000', selected: false},
						{label: '5000+', value: '5000', selected: false},
						{label: '1万+', value: '10000', selected: false},
						{label: '5万+', value: '50000', selected: false},
						{label: '10万+', value: '100000', selected: false}
					],
					commission: [
						{label: '佣金比例', value: 'all', selected: true},
						{label: '5%-10%', value: '5-10', selected: false},
						{label: '10%-20%', value: '10-20', selected: false},
						{label: '20%-30%', value: '20-30', selected: false},
						{label: '30%-50%', value: '30-50', selected: false},
						{label: '50%以上', value: '50+', selected: false}
					],
					sort: [
						{label: '最新上架', value: 'newest', selected: true},
						{label: '销量最高', value: 'sales', selected: false},
						{label: '佣金最高', value: 'commission', selected: false},
						{label: '价格最低', value: 'price_low', selected: false},
						{label: '价格最高', value: 'price_high', selected: false},
						{label: '好评最多', value: 'rating', selected: false}
					]
				}
			};
		},
		onLoad() {
			this.loadData();
		},
		//下拉刷新
		onPullDownRefresh(){
			this.recommendParams.pageNum=1;
			this.loadData();
		},
		//加载更多
		onReachBottom(){
			this.recommendParams.pageNum++;
			this.loadingType = 'loading';
			fetchRecommendProductList(this.recommendParams).then(response => {
				let addProductList = response.data;
				if(response.data.length===0){
					//没有更多了
					this.recommendParams.pageNum--;
					this.loadingType = 'nomore';
				}else{
					this.recommendProductList = this.recommendProductList.concat(addProductList);
					this.loadingType = 'more';
				}
			})
		},
		computed: {
			cutDownTime() {
				let endTime = new Date(this.homeFlashPromotion.endTime);
				let endDateTime = new Date();
				let startDateTime = new Date();
				endDateTime.setHours(endTime.getHours());
				endDateTime.setMinutes(endTime.getMinutes());
				endDateTime.setSeconds(endTime.getSeconds());
				let offsetTime = (endDateTime.getTime() - startDateTime.getTime());
				let endHour = Math.floor(offsetTime / (60 * 60 * 1000));
				let offsetMinute = offsetTime % (60 * 60 * 1000);
				let endMinute = Math.floor(offsetMinute / (60 * 1000));
				let offsetSecond = offsetTime % (60 * 1000);
				let endSecond = Math.floor(offsetSecond / 1000);
				return {
					endHour: endHour,
					endMinute: endMinute,
					endSecond: endSecond
				}
			}
		},
		filters: {
			formatTime(time) {
				if (time == null || time === '') {
					return 'N/A';
				}
				let date = new Date(time);
				return formatDate(date, 'hh:mm:ss')
			},
		},
		methods: {
			/**
			 * 加载数据
			 */
			async loadData() {
				fetchContent().then(response => {
					console.log("onLoad", response.data);
					this.advertiseList = response.data.advertiseList;
					this.swiperLength = this.advertiseList.length;
					this.titleNViewBackground = this.titleNViewBackgroundList[0];
					this.brandList = response.data.brandList;
					this.homeFlashPromotion = response.data.homeFlashPromotion;
					this.newProductList = response.data.newProductList;
					this.hotProductList = response.data.hotProductList;
					fetchRecommendProductList(this.recommendParams).then(response => {
						this.recommendProductList = response.data;
						uni.stopPullDownRefresh();
					})
				});
			},
			//轮播图切换修改背景色
			swiperChange(e) {
				const index = e.detail.current;
				this.swiperCurrent = index;
				let changeIndex = index % this.titleNViewBackgroundList.length;
				this.titleNViewBackground = this.titleNViewBackgroundList[changeIndex];
			},
			//商品详情页
			navToDetailPage(item) {
				let id = item.id;
				uni.navigateTo({
					url: `/pages/product/product?id=${id}`
				})
			},
			//广告详情页
			navToAdvertisePage(item) {
				let id = item.id;
				console.log("navToAdvertisePage",item)
			},
			//品牌详情页
			navToBrandDetailPage(item) {
				let id = item.id;
				uni.navigateTo({
					url: `/pages/brand/brandDetail?id=${id}`
				})
			},
			//推荐品牌列表页
			navToRecommendBrandPage() {
				uni.navigateTo({
					url: `/pages/brand/list`
				})
			},
			//新鲜好物列表页
			navToNewProudctListPage() {
				uni.navigateTo({
					url: `/pages/product/newProductList`
				})
			},
			//人气推荐列表页
			navToHotProudctListPage() {
				uni.navigateTo({
					url: `/pages/product/hotProductList`
				})
			},
			// 新增AI相关方法
			navToAIPage(page) {
				if (page === 'hotRanking') {
					uni.navigateTo({
						url: '/pages/ai/hotRankingList'
					});
				} else {
					this.showDeveloping();
				}
			},
			showDeveloping() {
				uni.showToast({
					title: '功能正在开发中',
					icon: 'none'
				});
			},
			// 星选专享按钮点击
			navToStarSelection() {
				const title = encodeURIComponent('星选专享');
				const type = 'star';
				uni.navigateTo({
					url: `/pages/ai/starProducts?title=${title}&type=${type}`
				});
			},
			// 四宫格季节页面跳转 - 统一跳转到starProducts页面，携带不同参数
			navToSeasonPage(item) {
				const title = encodeURIComponent(item.tag);
				const type = item.type;
				uni.navigateTo({
					url: `/pages/ai/starProducts?title=${title}&type=${type}`
				});
			},
			// 筛选相关方法
			showFilterDropdown(type) {
				this.currentFilterType = type;
				this.currentOptions = [...this.filterOptions[type]];
				
				// 设置下拉框标题
				const titles = {
					platform: '选择平台',
					sales: '选择销量范围',
					commission: '选择佣金比例',
					sort: '选择排序方式'
				};
				this.dropdownTitle = titles[type];
				this.showDropdown = true;
			},
			hideDropdown() {
				this.showDropdown = false;
			},
			selectOption(option) {
				// 清除当前类型的所有选中状态
				this.filterOptions[this.currentFilterType].forEach(item => {
					item.selected = false;
				});
				
				// 设置新的选中状态
				option.selected = true;
				
				// 更新当前筛选显示
				this.currentFilters[this.currentFilterType] = option.label;
				
				// 关闭下拉框
				this.hideDropdown();
				
				// 执行筛选逻辑
				this.applyFilter();
			},
			applyFilter() {
				// 这里可以根据筛选条件重新加载商品数据
				const filters = {};
				Object.keys(this.filterOptions).forEach(key => {
					const selected = this.filterOptions[key].find(item => item.selected);
					if (selected && selected.value !== 'all') {
						filters[key] = selected.value;
					}
				});
				
				console.log('应用筛选条件:', filters);
				uni.showToast({
					title: '筛选功能正在开发中',
					icon: 'none'
				});
			},
		},
		// #ifndef MP
		// 标题栏input搜索框点击
		onNavigationBarSearchInputClicked: async function(e) {
			this.$api.msg('点击了搜索框');
		},
		//点击导航栏 buttons 时触发
		onNavigationBarButtonTap(e) {
			const index = e.index;
			if (index === 0) {
				this.$api.msg('点击了扫描');
			} else if (index === 1) {
				// #ifdef APP-PLUS
				const pages = getCurrentPages();
				const page = pages[pages.length - 1];
				const currentWebview = page.$getAppWebview();
				currentWebview.hideTitleNViewButtonRedDot({
					index
				});
				// #endif
				uni.navigateTo({
					url: '/pages/notice/notice'
				})
			}
		}
		// #endif
	}
</script>

<style lang="scss">
	/* #ifdef MP */
	.mp-search-box {
		position: absolute;
		left: 0;
		top: 30upx;
		z-index: 9999;
		width: 100%;
		padding: 0 80upx;

		.ser-input {
			flex: 1;
			height: 56upx;
			line-height: 56upx;
			text-align: center;
			font-size: 28upx;
			color: $font-color-base;
			border-radius: 20px;
			background: rgba(255, 255, 255, .6);
		}
	}

	page {
		.cate-section {
			position: relative;
			z-index: 5;
			border-radius: 16upx 16upx 0 0;
			margin-top: -20upx;
		}

		.carousel-section {
			padding: 0;

			.titleNview-placing {
				padding-top: 0;
				height: 0;
			}

			.carousel {
				.carousel-item {
					padding: 0;
				}
			}

			.swiper-dots {
				left: 45upx;
				bottom: 40upx;
			}
		}
	}

	/* #endif */


	page {
		background: #f5f5f5;
	}

	.m-t {
		margin-top: 16upx;
	}

	/* 头部 轮播图 */
	.carousel-section {
		position: relative;
		padding-top: 10px;

		.titleNview-placing {
			height: var(--status-bar-height);
			padding-top: 44px;
			box-sizing: content-box;
		}

		.titleNview-background {
			position: absolute;
			top: 0;
			left: 0;
			width: 100%;
			height: 426upx;
			transition: .4s;
		}
	}

	.carousel {
		width: 100%;
		height: 350upx;

		.carousel-item {
			width: 100%;
			height: 100%;
			padding: 0 28upx;
			overflow: hidden;
		}

		image {
			width: 100%;
			height: 100%;
			border-radius: 10upx;
		}
	}

	.swiper-dots {
		display: flex;
		position: absolute;
		left: 60upx;
		bottom: 15upx;
		width: 72upx;
		height: 36upx;
		background-image: url(data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAMgAAABkCAYAAADDhn8LAAAAGXRFWHRTb2Z0d2FyZQBBZG9iZSBJbWFnZVJlYWR5ccllPAAAAyZpVFh0WE1MOmNvbS5hZG9iZS54bXAAAAAAADw/eHBhY2tldCBiZWdpbj0i77u/IiBpZD0iVzVNME1wQ2VoaUh6cmVTek5UY3prYzlkIj8+IDx4OnhtcG1ldGEgeG1sbnM6eD0iYWRvYmU6bnM6bWV0YS8iIHg6eG1wdGs9IkFkb2JlIFhNUCBDb3JlIDUuNi1jMTMyIDc5LjE1OTI4NCwgMjAxNi8wNC8xOS0xMzoxMzo0MCAgICAgICAgIj4gPHJkZjpSREYgeG1sbnM6cmRmPSJodHRwOi8vd3d3LnczLm9yZy8xOTk5LzAyLzIyLXJkZi1zeW50YXgtbnMjIj4gPHJkZjpEZXNjcmlwdGlvbiByZGY6YWJvdXQ9IiIgeG1sbnM6eG1wTU09Imh0dHA6Ly9ucy5hZG9iZS5jb20veGFwLzEuMC9tbS8iIHhtbG5zOnN0UmVmPSJodHRwOi8vbnMuYWRvYmUuY29tL3hhcC8xLjAvc1R5cGUvUmVzb3VyY2VSZWYjIiB4bWxuczp4bXA9Imh0dHA6Ly9ucy5hZG9iZS5jb20veGFwLzEuMC8iIHhtcE1NOkRvY3VtZW50SUQ9InhtcC5kaWQ6OTk4MzlBNjE0NjU1MTFFOUExNjRFQ0I3RTQ0NEExQjMiIHhtcE1NOkluc3RhbmNlSUQ9InhtcC5paWQ6OTk4MzlBNjA0NjU1MTFFOUExNjRFQ0I3RTQ0NEExQjMiIHhtcDpDcmVhdG9yVG9vbD0iQWRvYmUgUGhvdG9zaG9wIENDIDIwMTcgKFdpbmRvd3MpIj4gPHhtcE1NOkRlcml2ZWRGcm9tIHN0UmVmOmluc3RhbmNlSUQ9InhtcC5paWQ6Q0E3RUNERkE0NjExMTFFOTg5NzI4MTM2Rjg0OUQwOEUiIHN0UmVmOmRvY3VtZW50SUQ9InhtcC5kaWQ6Q0E3RUNERkI0NjExMTFFOTg5NzI4MTM2Rjg0OUQwOEUiLz4gPC9yZGY6RGVzY3JpcHRpb24+IDwvcmRmOlJERj4gPC94OnhtcG1ldGE+IDw/eHBhY2tldCBlbmQ9InIiPz4Gh5BPAAACTUlEQVR42uzcQW7jQAwFUdN306l1uWwNww5kqdsmm6/2MwtVCp8CosQtP9vg/2+/gY+DRAMBgqnjIp2PaCxCLLldpPARRIiFj1yBbMV+cHZh9PURRLQNhY8kgWyL/WDtwujjI8hoE8rKLqb5CDJaRMJHokC6yKgSCR9JAukmokIknCQJpLOIrJFwMsBJELFcKHwM9BFkLBMKFxNcBCHlQ+FhoocgpVwwnv0Xn30QBJGMC0QcaBVJiAMiec/dcwKuL4j1QMsVCXFAJE4s4NQA3K/8Y6DzO4g40P7UcmIBJxbEesCKWBDg8wWxHrAiFgT4fEGsB/CwIhYE+AeBAAdPLOcV8HRmWRDAiQVcO7GcV8CLM8uCAE4sQCDAlHcQ7x+ABQEEAggEEAggEEAggEAAgQACASAQQCCAQACBAAIBBAIIBBAIIBBAIABe4e9iAe/xd7EAJxYgEGDeO4j3EODp/cOCAE4sYMyJ5cwCHs4rCwI4sYBxJ5YzC84rCwKcXxArAuthQYDzC2JF0H49LAhwYUGsCFqvx5EF2T07dMaJBetx4cRyaqFtHJ8EIhK0i8OJBQxcECuCVutxJhCRoE0cZwMRyRcFefa/ffZBVPogePihhyCnbBhcfMFFEFM+DD4m+ghSlgmDkwlOgpAl4+BkkJMgZdk4+EgaSCcpVX7bmY9kgXQQU+1TgE0c+QJZUUz1b2T4SBbIKmJW+3iMj2SBVBWz+leVfCQLpIqYbp8b85EskIxyfIOfK5Sf+wiCRJEsllQ+oqEkQfBxmD8BBgA5hVjXyrBNUQAAAABJRU5ErkJggg==);
		background-size: 100% 100%;

		.num {
			width: 36upx;
			height: 36upx;
			border-radius: 50px;
			font-size: 24upx;
			color: #fff;
			text-align: center;
			line-height: 36upx;
		}

		.sign {
			position: absolute;
			top: 0;
			left: 50%;
			line-height: 36upx;
			font-size: 12upx;
			color: #fff;
			transform: translateX(-50%);
		}
	}

	/* AI功能导航区 */
	.ai-nav-section {
		background: #fff;
		padding: 30upx;
		margin-top: -20upx;
		border-radius: 16upx 16upx 0 0;
		position: relative;
		z-index: 5;
		
		.nav-grid {
			display: grid;
			grid-template-columns: repeat(4, 1fr);
			gap: 30upx;
			
			.nav-item {
				display: flex;
				flex-direction: column;
				align-items: center;
				
				.nav-icon {
					width: 88upx;
					height: 88upx;
					border-radius: 16upx;
					display: flex;
					align-items: center;
					justify-content: center;
					font-size: 40upx;
					margin-bottom: 10upx;
					
					&.hot {
						background: linear-gradient(135deg, #ff6b6b, #ff8e8e);
					}
					
					&.expert {
						background: linear-gradient(135deg, #4ecdc4, #44a08d);
					}
					
					&.live {
						background: linear-gradient(135deg, #a8edea, #fed6e3);
					}
					
					&.video {
						background: linear-gradient(135deg, #ffecd2, #fcb69f);
					}
					
					&.avatar {
						background: linear-gradient(135deg, #667eea, #764ba2);
					}
					
					&.ai-video {
						background: linear-gradient(135deg, #f093fb, #f5576c);
					}
					
					&.ai-copy {
						background: linear-gradient(135deg, #4facfe, #00f2fe);
					}
					
					&.ai-tools {
						background: linear-gradient(135deg, #43e97b, #38f9d7);
					}
				}
				
				text {
					font-size: 24upx;
					color: $font-color-dark;
				}
			}
		}
	}
	
	/* 星选专享区域 - 修改为左侧按钮+右侧四宫格 */
	.star-exclusive-section {
		display: flex;
		background: #fff;
		margin-top: 20upx;
		padding: 30upx;
		
		.star-button {
			width: 160upx;
			height: 320upx;
			display: flex;
			align-items: center;
			justify-content: center;
			background: linear-gradient(135deg, #667eea, #764ba2);
			border-radius: 16upx;
			margin-right: 20upx;
			
			.vertical-text {
				writing-mode: vertical-lr;
				font-size: 36upx;
				font-weight: bold;
				color: #fff;
				letter-spacing: 8upx;
			}
		}
		
		.star-grid {
			flex: 1;
			display: grid;
			grid-template-columns: 1fr 1fr;
			grid-template-rows: 1fr 1fr;
			gap: 15upx;
			height: 320upx;
			
			.grid-item {
				position: relative;
				border-radius: 12upx;
				overflow: hidden;
				
				.grid-tag {
					position: absolute;
					top: 8upx;
					left: 8upx;
					padding: 6upx 12upx;
					border-radius: 16upx;
					font-size: 18upx;
					color: #fff;
					z-index: 2;
					
					&.winter-tag {
						background: linear-gradient(135deg, #4a90e2, #357abd);
					}
					
					&.season-tag {
						background: linear-gradient(135deg, #7ed321, #5cb85c);
					}
					
					&.sale-tag {
						background: linear-gradient(135deg, #f5a623, #f39c12);
					}
					
					&.video-tag {
						background: linear-gradient(135deg, #d0021b, #e74c3c);
					}
				}
				
				.grid-image {
					width: 100%;
					height: 100%;
					object-fit: cover;
				}
				
				.grid-label {
					position: absolute;
					bottom: 8upx;
					left: 8upx;
					right: 8upx;
					text-align: center;
					font-size: 20upx;
					color: #fff;
					background: rgba(0, 0, 0, 0.5);
					padding: 6upx;
					border-radius: 8upx;
				}
			}
		}
	}

	.ad-1 {
		width: 100%;
		height: 210upx;
		padding: 10upx 0;
		background: #fff;

		image {
			width: 100%;
			height: 100%;
		}
	}

	/* 秒杀专区 */
	.seckill-section {
		padding: 4upx 30upx 24upx;
		background: #fff;

		.s-header {
			display: flex;
			align-items: center;
			height: 92upx;
			line-height: 1;

			.s-img {
				width: 140upx;
				height: 30upx;
			}

			.tip {
				font-size: $font-base;
				color: $font-color-light;
				margin: 0 20upx 0 40upx;
			}

			.timer {
				display: inline-block;
				width: 40upx;
				height: 36upx;
				text-align: center;
				line-height: 36upx;
				margin-right: 14upx;
				font-size: $font-sm+2upx;
				color: #fff;
				border-radius: 2px;
				background: rgba(0, 0, 0, .8);
			}

			.icon-you {
				font-size: $font-lg;
				color: $font-color-light;
				flex: 1;
				text-align: right;
			}
		}

		.floor-list {
			white-space: nowrap;
		}

		.scoll-wrapper {
			display: flex;
			align-items: flex-start;
		}

		.floor-item {
			width: 300upx;
			margin-right: 20upx;
			font-size: $font-sm+2upx;
			color: $font-color-dark;
			line-height: 1.8;

			image {
				width: 300upx;
				height: 300upx;
				border-radius: 6upx;
			}

			.price {
				color: $uni-color-primary;
			}
		}

		.title2 {
			font-size: $font-sm;
			color: $font-color-light;
			line-height: 40upx;
		}
	}

	.f-header {
		display: flex;
		align-items: center;
		height: 140upx;
		padding: 6upx 30upx 8upx;
		background: #fff;

		image {
			flex-shrink: 0;
			width: 80upx;
			height: 80upx;
			margin-right: 20upx;
		}

		.tit-box {
			flex: 1;
			display: flex;
			flex-direction: column;
		}

		.tit {
			font-size: $font-lg +2upx;
			color: #font-color-dark;
			line-height: 1.3;
		}

		.tit2 {
			font-size: $font-sm;
			color: $font-color-light;
		}

		.icon-you {
			font-size: $font-lg +2upx;
			color: $font-color-light;
		}

		.timer {
			display: inline-block;
			width: 40upx;
			height: 36upx;
			text-align: center;
			line-height: 36upx;
			margin-right: 14upx;
			font-size: $font-sm+2upx;
			color: #fff;
			border-radius: 2px;
			background: rgba(0, 0, 0, .8);
		}
	}

	/* 分类推荐楼层 */
	.hot-floor {
		width: 100%;
		overflow: hidden;
		margin-bottom: 20upx;

		.floor-img-box {
			width: 100%;
			height: 320upx;
			position: relative;

			&:after {
				content: '';
				position: absolute;
				left: 0;
				top: 0;
				width: 100%;
				height: 100%;
				background: linear-gradient(rgba(255, 255, 255, .06) 30%, #f8f8f8);
			}
		}

		.floor-img {
			width: 100%;
			height: 100%;
		}

		.floor-list {
			white-space: nowrap;
			padding: 20upx;
			padding-right: 50upx;
			border-radius: 6upx;
			margin-top: -140upx;
			margin-left: 30upx;
			background: #fff;
			box-shadow: 1px 1px 5px rgba(0, 0, 0, .2);
			position: relative;
			z-index: 1;
		}

		.scoll-wrapper {
			display: flex;
			align-items: flex-start;
		}

		.floor-item {
			width: 180upx;
			margin-right: 20upx;
			font-size: $font-sm+2upx;
			color: $font-color-dark;
			line-height: 1.8;

			image {
				width: 180upx;
				height: 180upx;
				border-radius: 6upx;
			}

			.price {
				color: $uni-color-primary;
			}
		}

		.more {
			display: flex;
			align-items: center;
			justify-content: center;
			flex-direction: column;
			flex-shrink: 0;
			width: 180upx;
			height: 180upx;
			border-radius: 6upx;
			background: #f3f3f3;
			font-size: $font-base;
			color: $font-color-light;

			text:first-child {
				margin-bottom: 4upx;
			}
		}
	}

	/* 猜你喜欢 */
	.guess-section {
		display: flex;
		flex-wrap: wrap;
		padding: 0 30upx;
		background: #fff;

		.guess-item {
			display: flex;
			flex-direction: column;
			width: 48%;
			padding-bottom: 40upx;

			&:nth-child(2n+1) {
				margin-right: 4%;
			}
		}

		.image-wrapper {
			width: 100%;
			height: 330upx;
			border-radius: 3px;
			overflow: hidden;

			image {
				width: 100%;
				height: 100%;
				opacity: 1;
			}
		}
		
		.image-wrapper-brand {
			width: 100%;
			height: 150upx;
			border-radius: 3px;
			overflow: hidden;
		
			image {
				width: 100%;
				height: 100%;
				opacity: 1;
			}
		}

		.title {
			font-size: $font-lg;
			color: $font-color-dark;
			line-height: 80upx;
		}

		.title2 {
			font-size: $font-sm;
			color: $font-color-light;
			line-height: 40upx;
		}

		.price {
			font-size: $font-lg;
			color: $uni-color-primary;
			line-height: 1;
		}
	}

	.hot-section {
		display: flex;
		flex-wrap: wrap;
		padding: 0 30upx;
		background: #fff;

		.guess-item {
			display: flex;
			flex-direction: row;
			width: 100%;
			padding-bottom: 40upx;
		}

		.image-wrapper {
			width: 30%;
			height: 250upx;
			border-radius: 3px;
			overflow: hidden;

			image {
				width: 100%;
				height: 100%;
				opacity: 1;
			}
		}

		.title {
			font-size: $font-lg;
			color: $font-color-dark;
			line-height: 80upx;
		}

		.title2 {
			font-size: $font-sm;
			color: $font-color-light;
			line-height: 40upx;
			height: 80upx;
			overflow: hidden;
			text-overflow: ellipsis;
			display: block;
		}

		.price {
			font-size: $font-lg;
			color: $uni-color-primary;
			line-height: 80upx;
		}

		.txt {
			width: 70%;
			display: flex;
			flex-direction: column;
			padding-left: 40upx;
		}
	}
	
	/* 筛选区域样式 */
	.filter-section {
		background: #fff;
		margin-top: 20upx;
		padding: 30upx;
		
		.filter-row {
			display: flex;
			justify-content: space-between;
			
			.filter-item {
				flex: 1;
				display: flex;
				align-items: center;
				justify-content: center;
				padding: 20upx 15upx;
				background: #f8f9fa;
				border-radius: 8upx;
				margin: 0 8upx;
				
				&:first-child {
					margin-left: 0;
				}
				
				&:last-child {
					margin-right: 0;
				}
				
				.filter-label {
					font-size: 26upx;
					color: #333;
					margin-right: 8upx;
				}
				
				.filter-arrow {
					font-size: 20upx;
					color: #666;
					transition: transform 0.3s;
				}
				
				&:active {
					background: #e9ecef;
				}
			}
		}
	}
	
	/* 下拉选择弹窗样式 */
	.dropdown-mask {
		position: fixed;
		top: 0;
		left: 0;
		right: 0;
		bottom: 0;
		background: rgba(0, 0, 0, 0.5);
		z-index: 1000;
		display: flex;
		align-items: flex-end;
		opacity: 0;
		visibility: hidden;
		transition: all 0.3s;
		
		&.show {
			opacity: 1;
			visibility: visible;
		}
		
		.dropdown-content {
			width: 100%;
			background: #fff;
			border-radius: 20upx 20upx 0 0;
			max-height: 60vh;
			transform: translateY(100%);
			transition: transform 0.3s;
		}
		
		&.show .dropdown-content {
			transform: translateY(0);
		}
		
		.dropdown-title {
			text-align: center;
			padding: 30upx;
			font-size: 32upx;
			font-weight: bold;
			color: #333;
			border-bottom: 1px solid #f0f0f0;
		}
		
		.dropdown-list {
			max-height: 400upx;
			overflow-y: auto;
			
			.dropdown-option {
				display: flex;
				align-items: center;
				justify-content: space-between;
				padding: 30upx;
				border-bottom: 1px solid #f8f9fa;
				
				&:last-child {
					border-bottom: none;
				}
				
				&.active {
					background: #f0f8ff;
					color: #1890ff;
				}
				
				text {
					font-size: 28upx;
				}
				
				.check-icon {
					color: #1890ff;
					font-weight: bold;
				}
				
				&:active {
					background: #f5f5f5;
				}
			}
		}
	}
</style>
