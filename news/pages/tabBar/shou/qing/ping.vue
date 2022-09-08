<template>
	<view>
		<view class="texta">
			<textarea value="" placeholder="请输入所需内容……" v-model="formData.content" />
		</view>
		<view class="btn-v">
			<button type="default" class="btn" @click="addMessage">发布</button>
		</view>
		<view class="content">
			<!-- {{formData.countent}} -->
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				logined: false, //没有登陆
				formData: {
					news_id: null,
					content: ''
				}
			}
		},
		onLoad(option) {

			this.formData.news_id = option.newsId
		},
		methods: {
			//发布留言
			addMessage() {
				//进行表单的验证，未填信息应提醒
				//从缓存读取缓存token，使用同步方式，请区分同步和异步的差别
				//提交信息到服务器
				// console.log(this.formData)
				uni.showLoading({
					title: '加载中'
				});
				let tk = uni.getStorageSync('token'); //从本地缓存中同步获取指定的key对应的内容
				uni.request({
					url: 'http://47.107.83.107:9093/api/comment/add',
					data: this.formData,
					header: {
						'Authorization': tk
					},
					method: 'POST',
					success: (res) => {
						var result = res.data;
						// console.log(result)
						if (result.code = 200) {
							uni.showToast({
								title: result.msg,
								duration: 2000
							});
							uni.navigateBack();
							uni.$emit('updateTopic')
						} else {
							alert(result.msg);
						}
					}
				});
			}
		},

		created() {
			//从缓存中读取之前登录成功后存储的token
			//token不存在，说明还未登陆,应提醒用户再进行跳转
			//应传递参数给登录页，以便在登录后知道返回那个页面，发布页还是用户中心页
			uni.getStorage({
				key: 'token',
				fail: () => {
					// alert('登录后才能发布留言哦');
					uni.redirectTo({
						url: '../../wo/login/login'
					});
				}
			});
		},
	}
</script>

<style>
	.texta {
		width: 100%;
		height: 500rpx;
		border: #C0C0C0 1px solid;
	}

	.btn-v {
		display: flex;
		margin-top: 30rpx;
	}

	.btn {
		height: 84rpx;
		border-radius: 35rpx;
		font-size: 40rpx;
		line-height: 84rpx;
		background: linear-gradient(left, #ff978d, #ffbb69);
		margin-top: 8rpx;
		width: 180rpx;
		background-color: #007AFF;
	}
</style>
