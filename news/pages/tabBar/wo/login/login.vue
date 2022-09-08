<template>
	<view class="box">
		<view class="content">
			<view class="header">
				<image class="logo" src="/static/my.png"></image>
			</view>
			<h2>用户登录</h2>
		</view>
		<view class="footer">
			<input type="text" placeholder="请输入账号" v-model="account" />
			<input class="in1" type="text" placeholder="请输入密码" v-model="password" />
			<button @click="btn">登录</button>
			<view class="rigth">
				<text> 没有账号，点击<a  @click="zhu">注册</a></text>
			</view>

		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				account: "", //账号
				password: "" //密码
			}
		},
		onLoad() {

		},
		methods: {
			btn() {
				uni.showLoading({
					title: '加载中'
				});
				uni.request({
					url: "http://47.107.83.107:9093/api/account/login",
					method: "POST",
					data: {
						account: this.account,
						password:this.password
					},
					success: (res) => {
							var result =res.data
							if(result.code==200){
								uni.setStorage({
									key:'token',
									data:result.data.token
								});
								uni.showToast({
									title: result.msg,
									duration: 2000
								});
								setTimeout(function() {
								         uni.reLaunch({
								         	url:"../wo"
								         })
								     }, 1000);
							}else if(result.code==401){
								uni.showToast({
									title: result.msg,
									icon:"error",
									duration: 2000
								});
							}
							
					},
				});
			},
			zhu(){
				uni.navigateTo({
					url:"./zhu"
				})
			},
			onLoad(option){
				//预防没有传back参数情况
				if(option.hasOwnProperty("back")){
					this.back = option.back;
				}else{
					this.back = '';
				}
			},
				
		}
	}
</script>

<style scoped>
	page {
		background-color: #f8f8f8;
	}

	.content {
		height: 160px;
		padding-top: 40px;
		background-color: #0055ff;
	}

	.content h2 {
		color: #ffffff;
		text-align: center;
	}

	.header {
		width: 100px;
		margin: 0 auto;
	}

	.logo {
		height: 100px;
		width: 100px;
	}

	.footer {
		margin-top: 70px;
	}

	.footer input {
		width: 84%;
		height: 50px;
		margin: 0 auto;
		margin-top: 20px;
		background: #ffffff;
		box-sizing: border-box;
		border-radius: 30px;
		text-indent: 36px;
		background-image: url(../../../../static/zhanghao.png);
		background-position: 8px 14px;
		background-size: 22px;
		background-repeat: no-repeat;
	}

	.footer .in1 {
		background-image: url(../../../../static/mima.png);
	}

	.footer button {
		width: 84%;
		height: 50px;
		border-radius: 30px;
		background-color: red;
		color: #ffffff;
		margin-top: 30px;
	}

	.rigth {
		text-align: center;
		margin-left: 40%;
		margin-top: 6px;
	}

	.rigth a {
		color: #0055FF;
		text-decoration: underline;
	}
</style>
