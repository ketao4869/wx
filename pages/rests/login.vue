<template>
	<view>
		<!-- 状态导航栏(原导航栏) -->
		<cu-custom bgColor="bg-gradual-blue" :isBack="true">
			<block slot="backText">返回</block>
			<block slot="content">登录</block>
		</cu-custom>
		
		<button style="margin-top:100px;" type="primary" open-type="getUserInfo" @getuserinfo="getuserinfo" withCredentials="true">微信登录</button>
		
		<view class="cu-tabbar-height"></view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				
			}
		},
		methods: {
			//微信小程序登录
			getuserinfo : function(res1) {
				uni.showToast({
					title: '登录1',
					icon: 'none'
				});
				if(!res1.detail.iv){
					uni.showToast({
						title: '您取消了授权,登录失败',
						icon: 'none'
					});
					return false;
				}
				uni.login({
					provider: 'weixin',
					success: function (res2) {
						console.log(res2)
						uni.showToast({
							title: '登录2',
							icon: 'none'
						});
						uni.request({
							method:'POST',
							header : {'content-type':'application/x-www-form-urlencoded'},
							url: 'http://admin.ke-tao.top/uni-app/wx-api.php',
							data:{code:res2.code},
							success:function(res3){
								uni.showToast({
									title: '登录3',
									icon: 'none'
								});
								uni.request({
									method:'POST',
									url:'http://admin.ke-tao.top/uni-app/PHP/',
									header : {'content-type':'application/x-www-form-urlencoded'},
									data : {
										// appid : "wx9e50bec0a682477c",
										sessionKey : res3.data.session_key,
										iv : res1.detail.iv,
										encryptedData : res1.detail.encryptedData
									},
									success:function(res4){
										console.log(res4)
										uni.showToast({
											title: '登录4',
											icon: 'none'
										});
										if(res4.data === -41003){
											// console.log('解密错误');
											uni.showToast({
												title: '请重新登录',
												image: '../../static/icon/error.png'
											});
											return false;
										}
										uni.request({
											url:'http://admin.ke-tao.top/uni-app/wx-users-login.php',
											method:'POST',
											header : {'content-type':'application/x-www-form-urlencoded'},
											data:{
												openId: res4.data.openId,
												nickName: res4.data.nickName,
												gender: res4.data.gender,
												language: res4.data.language,
												city: res4.data.city,
												province: res4.data.province,
												country: res4.data.country,
												avatarUrl: res4.data.avatarUrl,
												unionId: res4.data.unionId,
											},
											success:function(res5){
												uni.showToast({
													title: '登录5',
													icon: 'none'
												});
												console.log(res5);
												try{
													uni.setStorageSync('s_uid', res5.data[0].s_uid);
													uni.setStorageSync('srand', res5.data[0].srand);
													uni.setStorageSync('name', res5.data[0].nickName);
													uni.setStorageSync('avatarUrl', res5.data[0].avatarUrl);
													uni.setStorageSync('login', 'true');
													uni.showToast({
														title: '登录成功',
														image: '../../static/icon/success.png'
													});
													setTimeout(function(){
														uni.$emit('login', {
															avatarUrl: res5.data[0].avatarUrl,
															name: res5.data[0].nickName,
															login: true
														});
														uni.navigateBack({});
													},500);
												}catch(e){
													console.log(e);
													uni.showToast({
														title: '写入内存失败，登录失败',
														image: '../../static/icon/error.png'
													});
												}
											}
										});
									}
								})
							},
							fail: (err) => {
								
								uni.showToast({
									title: '错误1',
									icon: 'none'
								});
							}
						});
					},
				});
			}
		}
	}
</script>

<style>

</style>
