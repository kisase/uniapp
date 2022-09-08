<template>
	<view class="box">
		<view class="content">
			<view class="header">
				<image class="logo" src="/static/my.png"></image>
			</view>
			<h2>用户注册</h2>
		</view>
		<view class="footer">
			<input type="text" placeholder="请输入账号" v-model="account" />
			<text v-if="ifs">用户已存在</text>
			<input class="in1" type="text" placeholder="请输入密码" v-model="password" />
			<button @click="btn">注册</button>
			<button class="b" @click="btn1">已有帐号，返回登录</button>

		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				account: "", //账号
				password: "" ,//密码
				ifs:false
			}
		},
		onLoad() {

		},
		methods: {
			btn() {
				if (this.account == "" || this.password == "") {
					uni.showToast({
						title: "账号或密码为空",
						icon:"error",
						duration: 2000

					});
				} else {
					uni.showLoading({
						title: '加载中'
					});
					uni.request({
						url: 'http://47.107.83.107:9093/api/account/register',
						method: 'POST',
						data: {
							account: this.account,
							password: this.password
						},
						success: (res) => {
							var result = res.data;
							// console.log(res)
							if (result.code == 200) {
								uni.showToast({
									title: result.msg,
									duration: 2000
								});
								setTimeout(function() {
									uni.navigateTo({
										url: "./login"
									})
								}, 1000);
							} else if (result.code == 422) {
								uni.showToast({
									title: result.msg,
									duration: 2000
								});
							}
						}
					})
				}

			},
			btn1() {
				uni.redirectTo({
					url: "./login"
				})
			},
			f(){
				this.ifs=true
			},
			ff(){
				this.ifs=false
			}
		},
		watch: {
			'account': function(res){
				uni.request({
						url: 'http://47.107.83.107:9093/api/account/accountExist?account=test1',
						method: 'GET',
						data: {
							account: res,
						},
						success: (res) => {
							var result = res.data;
							if (result.code == 200) {
								this.$nextTick(function(){
									this.f()
								})
							}else{
								this.$nextTick(function(){
									this.ff()
								})
							}
						}
					})
				}
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

	.footer .b {
		background-color: #C0C0C0;
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
