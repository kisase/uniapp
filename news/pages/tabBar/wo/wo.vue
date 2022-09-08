<template>
	<view>
		<view class="header" v-show="show==true">
			<view class="left">
				<image src="../../../static/my.png" mode=""></image>
			</view>
			<view class="right" @click="btn">
				<text>你还未登陆</text>
			</view>
		</view>
		<view class="ff" v-show="show!=true">
		<view class="header" >
			<view class="left">
				<image :src=avatar mode=""></image>
			</view>
			<view class="right">
				<text>{{account}}</text>
			</view>
		</view>
		<button type="default" @click="logout()">退出登录</button>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				show:true,
				account:"",
				avatar:''
			}
		},
		onLoad(){
			this.created()
			
		},
		methods: {
			btn(){
				uni.navigateTo({
					url:"./login/login"
				})
			},
			logout(){//注销
			this.show=true
				uni.removeStorage({
					key:'token',
					success:function(res) {
						uni.navigateTo({
							url:'./login/login'
						})
					}
				})
				
			},
			getInfo(tk){
				uni.request({
					url:'http://47.107.83.107:9093/api/account/getInfo',
					method:'POST',
					data:{},
					header:{
						'Authorization':tk
					},
					success: (res) => {
						var result=res.data;
						// console.log(result)
						if(result.code==200){
							this.account=result.data.account;
							this.avatar=result.data.avatar;
							//保存用户账号和头像
							uni.setStorage({
								key:'role',
								data:result.data.role
							});
						}else if(result.code==401){
							alert(result.msg);
							console("没有这个账号");
							uni.redirectTo({
								url:'../../user/login/login'
							})
						}
					}
				})
			},
			created(){
				//从缓存中读取之前登陆成功后存储token
				var token=null;
				uni.getStorage({
					key:'token',
					success:function(res){
						token=res.data;
					}
				});
				this.show=token!=null?false:true;
				if(this.show==false){
					this.getInfo(token);
				}
			}
		}
		
	}
</script>

<style lang="scss">
.header{
	height: 160px;
	background-color: #0055FF;
	image{
		width: 90px;
		height: 90px;
	}
	.left{
		float: left;
		margin: 30px;
		
	}
	.right{
		float: left;
		margin-top: 35px;
		padding: 30px 30px 30px 0;
		box-sizing: border-box;
		color: #FFFFFF;
		font-size: 20px;
		
	}
}
.ff image{
	border-radius: 50%;
}
.ff button{
	margin-top: 100px;
}
</style>
