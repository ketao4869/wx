<script>
global.isLogin = function() {
	try {
		var suid = uni.getStorageSync('s_uid');//id
		var srand = uni.getStorageSync('srand');//随机码
		var name = uni.getStorageSync('name');//用户名
		var avatarUrl = uni.getStorageSync('avatarUrl');//用户头像
		var login = uni.getStorageSync('login');//是否登录
	} catch (e) {
		//TODO handle the exception
	}
	if (suid == '' || srand == '' || avatarUrl == '' || name =='' || login =='') {
		return false;
	} else {
		return [name,avatarUrl,login,srand];
	}
};
	import Vue from 'vue'
	export default {
		onLaunch: function() {
			uni.getSystemInfo({//获取系统信息
				success: function(e) {
					console.log(e)
					// #ifndef MP
					// 所有小程序
					// Vue.prototype:表示该类的原型。您可以使用原型向类的所有实例添加属性或方法。
					//他不会污染全局变量，只在这个条件下使用
					Vue.prototype.StatusBar = e.statusBarHeight;//状态栏高度
					if (e.platform == 'android') {//判断手机类型
						Vue.prototype.CustomBar = e.statusBarHeight + 50;
					} else {
						Vue.prototype.CustomBar = e.statusBarHeight + 45;
					};
					// #endif

					// #ifdef MP-WEIXIN
					// 微信小程序
					Vue.prototype.StatusBar = e.statusBarHeight;
					//获取小程序右上角的悬浮按钮，坐标以屏幕左上角为原点。单位px
					let custom = wx.getMenuButtonBoundingClientRect();
					// console.log(custom);
					Vue.prototype.Custom = custom;
					//CustomBar高度=（小程序悬浮按钮）底部坐标+顶部坐标-状态栏高度
					Vue.prototype.CustomBar = custom.bottom + custom.top - e.statusBarHeight;
					//可用屏幕高度(因为设置了自定义导航栏，可用高度包括导航栏高度)
					let windowHeight = e.windowHeight;
					Vue.prototype.windowHeight = windowHeight;
					//可移动视图容器定位（添加页面“+”）
					let x = e.windowWidth - 100;
					let y = e.windowHeight - 270;
					Vue.prototype.x = x;
					Vue.prototype.y = y;
					// #endif		
				}
			});
			console.log('App onLaunch');
		},
		onShow: function() {
			console.log('App Show');
		},
		onHide: function() {
			console.log('App Hide');
		}

	}
</script>

<style>
	@import "colorui/main.css";
	@import "colorui/icon.css";
	@import "colorui/edit.css";
	
</style>
