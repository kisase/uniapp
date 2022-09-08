<template>
	<view class="content">
			<scroll-view scroll-y="true" class="scrollView1" @scrolltolower="loadpage">
				<swiper :indicator-dots="true" :autoplay="true" :interval="3000" :duration="1000">
					<swiper-item>
						<view class="swiper-item">
							<image
								src="http://www.cnsphoto.com/cnsphoto/groupPicToIo/getIoReadImg.do?picId=32035342&langType=0&imgType=4&featuresImgFlag=1"
								style="width: 100%;"></image>
						</view>
					</swiper-item>
					<swiper-item>
						<view class="swiper-item">
							<image
								src="http://www.cnsphoto.com/cnsphoto/groupPicToIo/getIoReadImg.do?picId=32030424&langType=0&imgType=4&featuresImgFlag=1"
								style="width: 100%;"></image>
						</view>
					</swiper-item>
					<swiper-item>
						<view class="swiper-item">
							<image
								src="http://www.cnsphoto.com/cnsphoto/groupPicToIo/getIoReadImg.do?picId=32027710&langType=0&imgType=4&featuresImgFlag=1"
								style="width: 100%;"></image>
						</view>
					</swiper-item>
					<swiper-item>
						<view class="swiper-item">
							<image
								src="http://www.cnsphoto.com/cnsphoto/groupPicToIo/getIoReadImg.do?picId=31990662&langType=0&imgType=4&featuresImgFlag=1"
								style="width: 100%;"></image>
						</view>
					</swiper-item>
				</swiper>
				<navigator @click="goToDetails(item)" v-for="(item,i) in list" :key="i">
					<view class="item1 flex1">
						<view class="item2 flex2">
							<view class="title">{{item.title}}</view>
							<view class="created-time">{{item.create_time}} 阅读量:{{item.total_hit}}次</view>
						</view>
						<view class="pic">
							<image :src="item.pics" mode="widthFix"></image>
						</view>
					</view>
				</navigator>
			</scroll-view>

	</view>
</template>

<script>
	export default {
		data() {
			return {
				list: [],
				news_id: 1,
				page: 1,
				countPage: 0,
			}
		},
		onLoad() {
			this.toLower()
		},
		methods: {
			loadpage(){
				this.page++
				this.toLower()
			},
			toLower() {
				uni.request({
					url: 'http://47.107.83.107:9093/api/news/getList/' + this.page + '/10',
					data: {},
					success: (res) => {
						this.list = this.list.concat(res.data.data.rows)
						this.countPage = Math.ceil(res.data.data / 10);
					}
				});
			},
			
			goToDetails(news) {
				uni.navigateTo({
					url: './qing/qing?newsId=' + news.news_id
				})
			}
		}
	}
</script>

<style lang="scss" scoped>
	.item {
		height: 240px;
	}

	.scrollView1 {
		width: 100%;
		height: 100vh;
		//border: 1px solid #000;margin: auto;
		.item1 {
			height: 100px;
			background: #F1F1F1;
			border-bottom: 1px solid #fff;
			display: flex;
			padding: 10rpx;
		}

		.flex1 {
			flex-direction: row;
		}

		.item2 {
			display: flex;
			flex: 1;
		}

		.flex2 {
			flex-direction: column;
		}

		.created-time {
			color: #8F939C;
			font-size: 20rpx;
		}
	}

	.title {
		flex: 2;
		margin-top: 8px;
		font-size: 22rpx;
		line-height: 22px;
		letter-spacing: 1px;
	}

	.pic {
		width: 250rpx;
		height: 100px;
		overflow: hidden;
	}

	.pic image {
		width: 250rpx;
		height: 100px;
	}
</style>
