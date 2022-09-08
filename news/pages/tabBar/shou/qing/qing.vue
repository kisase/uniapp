<template>
	<view class="newsBar">
		<view class="wrapper">
			<view class="heart">
			</view>
			<view class="main">
				<scroll-view scroll-y="true" class="scrollView" style="height: 100vh;">
					<view class="">
						<view class="page">
							<view class="title">
								{{news.title}}
							</view>
							<image :src="news.pics" style="width: 100%;" mode="widthFix"></image>
							<view class="content " v-html="news.content"></view>
							<view class="pinglun">
								<view class="item" v-for="(item,index) in list">
									<view class="left">
										<view class="commenter">{{item.account}}</view>
										<cover-image class="avatar" :src="item.avatar"></cover-image>
									</view>
									<view class="main1">
										<button type="default" @click="clear(item.id)"
											v-show="item.account_id == id">删除</button>
										<view class="content2">{{item.content}}</view>
										<view class="time">发表于:{{item.create_time}}</view>
									</view>
								</view>
								<p class="xian" @click="xian()">显示更多</p>
							</view>
						</view>
					</view>
				</scroll-view>
			</view>
			<view class="footer">
				<view class="footer-img footer-flex">
					<textarea value="" placeholder="发表神妙评论" placeholder-style="color:#fff;padding-left:45rpx"
						@click="gototopic" />
					<image src="../../../../static/take2.png" mode="" class="image"></image>
					<image :src="collectImg" mode="" @click="collect" class="image"></image>
					<image :src="goodImg" mode="" @click="good" class="image"></image>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				news: {},
				collectImg: require('../../../../static/collect0.png'),
				goodImg: require('../../../../static/good1.png'),
				token: uni.getStorageSync('token'),
				list: [],
				id: '',
				is_store: false,
				praiseAction: false,
				news_id:"",
				page:10,
				 tk: uni.getStorageSync('token')
			}

		},
		onLoad(option) {
			this.news_id=option.newsId
			this.geto()
			this.getNews(option.newsId);
			this.topiclist();
			uni.$on('updateTopic', () => {
				this.topiclist(option.newsId)
			})
		},
		methods: {
			//用户信息
			geto() {
				
				uni.request({
					url: 'http://47.107.83.107:9093/api/account/getInfo',
					method: 'POST',
					data: {},
					header: {
						'Authorization': this.tk
					},
					success: (res) => {
						var result = res.data;
						// console.log(result)
						if (result.code == 200) {
							// console.log(result)
							this.id = result.data.account_id;
						}
					}
				})
			},
			//点击发表评论内容
			gototopic() {
				if(this.tk==""){
					uni.showToast({
						title: "未登录，两秒自动跳转",
						duration: 2000,
						icon:"error"
					});
					setTimeout(()=>{
						uni.navigateTo({
							url: '../../wo/login/login'
						})
					},2000)
				}else{
					uni.navigateTo({
						url: './ping?newsId=' + this.news.news_id
					})
				}
				
			},
			//评论接口
			topiclist() {
				uni.request({
					url: 'http://47.107.83.107:9093/api/comment/getList/' + this.news_id + '/1/'+this.page,
					data: {},
					method: 'GET',
					success: (res) => {
						if (res.data.code == 200) {
							this.list = res.data.data.rows;
							// console.log(this.list)
						}
					}
				})
			},
			//删除评论
			clear(id) {
				let tk = uni.getStorageSync('token'); //从本地缓存中同步获取指定的key对应的内容
				uni.request({
					url: 'http://47.107.83.107:9093/api/comment/delete/' + id,
					data: {},
					header: {
						'Authorization': tk
					},
					method: 'GET',
					success: (res) => {
						if (res.data.code == 200) {
							uni.showToast({
								title: "删除成功",
								duration: 2000
							})
							this.topiclist()
						}
					}
				})
			},

			//更多评论
			xian(){
				this.page+=10
				this.topiclist()
			},
			//加入、取消收藏
			collect() {
				uni.showLoading({
					title: '加载中'
				});
				if(this.tk==""){
					uni.showToast({
						title: "请登录后,再操作",
						duration: 2000,
						icon:"error"
					});
				}else{
				uni.request({
					url: 'http://47.107.83.107:9093/api/news/storeAction',
					data: {
						news_id: this.news.news_id, //新闻id
						action: this.is_store ? 2 : 1 //加入还是取消，1=加入，2=取消
					},
					method: 'POST',
					header: {
						'Authorization': this.token,
						'Content-Type': 'application/json'
					},
					success: (res) => {
						if (res.data.code == 200) {
							this.is_store = !this.is_store;
							this.collectImg = this.is_store ? require('../../../../static/collect2.png') :
								require('../../../../static/collect0.png');
							if (this.is_store) {
								uni.showToast({
									title: "收藏成功",
									duration: 2000
								});
							} else {
								uni.showToast({
									title: "取消收藏",
									duration: 2000
								});
							}
						} else if (res.data.code == 405) {
							uni.showToast({
								title: res.data.msg,
								duration: 2000
							});
						}
					},
				})}
			},
			//加入、取消点赞
			good() {
				uni.showLoading({
					title: '加载中'
				});
				if(this.tk==""){
					uni.showToast({
						title: "请登录后,再操作",
						duration: 2000,
						icon:"error"
					});
				}else{
				uni.request({
					url: 'http://47.107.83.107:9093/api/news/praiseAction',
					data: {
						news_id: this.news.news_id, //新闻id
						action: this.praiseAction ? 2 : 1 //加入还是取消，1=加入，2=取消
					},
					method: 'POST',
					header: {
						'Authorization': this.token,
						'Content-Type': 'application/json'
					},
					success: (res) => {
						if (res.data.code == 200) {
							this.praiseAction = !this.praiseAction;
							this.goodImg = this.praiseAction ? require('../../../../static/good.png') :
								require('../../../../static/good1.png');
							if (this.praiseAction) {
								uni.showToast({
									title: "点赞成功",
									duration: 2000
								});
							} else {
								uni.showToast({
									title: "取消点赞",
									duration: 2000
								});

							}

						} else if (res.data.code == 405) {
							alert(res.data.msg);
						}
					}
				})}
			},
			//获取新闻
			getNews(newsId = 1) {
				uni.request({
					url: 'http://47.107.83.107:9093/api/news/getDetail/' + newsId,
					method: 'GET',
					success: (res) => {
						if (res.data.code == 200) {
							this.news = res.data.data;
						} else if (res.data.code == 401) {
							alert(res.data.msg)
						}
					}
				})
			}
		}
	}
</script>

<style lang="scss">
	.page {
		margin: 10px;
		line-height: 23px;
		height: calc(100% - 20px);
	}

	uni-page-body {
		height: 100%;
	}

	.newsBar {
		height: 100%;
	}

	.title {
		color: red;
		font-size: 18px;
		margin-bottom: 10px;
	}

	.content {
		margin-top: 20px;
	}

	.wrapper {
		height: 100%;
		width: 100%;
		position: relative;
	}

	.main {
		height: calc(100% - 150rpx);
		overflow-y: auto;
		button{
			width: 52px;
			margin-right: 0;
			font-size: 7px;
			line-height: 30px;
			
			
		}
	}

	/* .scrollView{
		position: absolute;
		top: 0;
		bottom: 0;
		left: 0;
		right: 0;
	} */
	.footer {
		background-color: #D8D8D8;
		color: #fff;
		line-height: 150rpx;
		position: absolute;
		bottom: 0;
		width: 100%;
	}

	textarea {
		border: 1rpx solid #FFFFFF;
		height: 75rpx;
		line-height: 75rpx;
		width: 48%;
		border-radius: 25rpx;
		margin-top: 15rpx;
		color: #18BC37;
	}

	.footer-img {
		display: flex;
	}

	.footer-flex {
		flex-direction: row;
	}

	.image {
		width: 85rpx;
		margin-left: 35rpx;
		height: 85rpx;
	}

	.item {
		display: flex;
		margin: 0 15rpx;
		padding: 15px 0;
		border-bottom: 1px solid #e2e2e2;
	}

	.left {
		width: 80px;
		margin-right: 10px;
	}

	.avatar {
		width: 60px;
		height: 60px;
		border-radius: 50%;
		margin:0 auto;
	}

	.commenter {
		text-align: center;
		font-size: 0.825rem;
		color: #777;

	}

	.main1 {
		flex: 1;
		margin-top: 10px;
	}

	.time {
		padding-bottom: 0;
		font-size: 28rpx;
		color: #C8C7CC;
		line-height: 15 0%;
	}

	.content2 {
		font-size: 32rpx;
		color: #333333;
	}
	.xian{
		text-align: center;
		border: 1px solid #999999;
		font-size: 16px;
		color: #6a6a6a;
	}
</style>
