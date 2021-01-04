<template>
	<view>
		<!-- 状态导航栏(原导航栏) -->
		<view class="bg-gradual-blue">
			<cu-custom></cu-custom>
		
			<view @click="goLogin" style="display:flex;padding: 0rpx 70rpx 30rpx 70rpx;">
				<image :src="userInfo.login ? userInfo.avatarUrl :avatarUrl" style="width:150rpx;height:150rpx;border-radius:150rpx;border:1px solid #000000;"></image>
				<view style="height:150rpx;justify-content: space-around;flex-direction: column;">
					<text style="height:150rpx;line-height:150rpx;" class="text-xll padding-left">Hi，{{userInfo.login ? userInfo.name : '您未登录'}}</text>
					<text style="padding-left:5rpx" class="cuIcon-right" v-if="!userInfo.login"></text>
				</view>
			</view>
		</view>
		
		<view class="cu-list menu card-menu margin">
			<view class="cu-item arrow">
				<view class="content">
					<text class="cuIcon-circlefill text-grey"></text>
					<text class="text-grey">帐号管理</text>
				</view>
			</view>
			<view class="cu-item arrow">
				<view class="content">
					<text class="cuIcon-circlefill text-grey"></text>
					<text class="text-grey">新消息通知</text>
				</view>
			</view>
			<view class="cu-item arrow">
				<view class="content">
					<text class="cuIcon-circlefill text-grey"></text>
					<text class="text-grey">帮助与反馈</text>
				</view>
			</view>
			<view class="cu-item arrow">
				<view class="content">
					<text class="cuIcon-circlefill text-grey"></text>
					<text class="text-grey">关于应用</text>
				</view>
			</view>
			<view class="cu-item arrow" @click="outlogin">
				<view class="content">
					<text class="cuIcon-circlefill text-grey"></text>
					<text class="text-grey">退出登录</text>
				</view>
			</view>
		</view>
		
		<view class="cu-tabbar-height"></view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				avatarUrl: "../../static/logo.png",
				userInfo: {
					name:'',//姓名
					avatarUrl:'',//头像地址
					login: false,
				}
			}
		},
		onReady(){
			console.log('阿萨德');
			var res = global.isLogin();
			if (!res) {
				uni.showModal({
					// 弹出可以确定取消的模态窗
				    title: '提示',
				    content: '你还未登录，请登录',
				    success: function (res) {
				        if (res.confirm) {
						// 如果点击确定,跳转到登录界面
							uni.navigateTo({
							    url: '/pages/rests/login'
							});
				        } else if (res.cancel) {
							// 如果点击取消,弹出提示,你取消了登录
							uni.showToast({
								title: '您取消了登录',
								icon: 'none'
							});
				        }
				    }
				});
			}else{
				// console.log(res[0]);
				let useINfo = {
					name:res[0],//姓名
					avatarUrl:res[1],//头像地址
					login: res[2],
				}
				this.userInfo = useINfo;
			}
		},
		onLoad() {//开启监听
			uni.$on('login', e => {
				console.log(e);
				this.userInfo = e;
			});
		},
		onUnload() {//移除监听
			uni.$off('login');
		},
		methods: {
			goLogin() {
				if (!this.userInfo.login) {//login为false跳转到登录界面
					uni.navigateTo({
						url: '../rests/login'
					});
				}else{
				console.log('你已经登录了');
				}
			},
			outlogin(){
				uni.clearStorage();
				this.userInfo = {
					name:'',//姓名
					avatarUrl:'',//头像地址
					login: false
				};
			}
		}
	}
</script>

<style>

</style>
