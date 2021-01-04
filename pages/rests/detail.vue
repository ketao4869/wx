<template>
	<view>
		<!-- 状态导航栏(原导航栏) -->
		<cu-custom bgColor="bg-gradual-blue" :isBack="true">
			<block slot="backText">返回</block>
			<block slot="content">预览</block>
		</cu-custom>
		<view class="margin-xs" style="float:right;">
			 <view>
				<button class="cu-btn bg-gradual-blue round" @tap="edit">编辑</button>
			 </view>
		</view>
		
		<view :style="{height:swiperHeight-52+'px'}" style="width:100%;margin:auto;">
			<scroll-view scroll-y style="width:100%;height:100%;" >
				<rich-text :nodes="richText"></rich-text>
			</scroll-view>
		</view>
	</view>
</template>

<script>
var _str;
	export default {
		data() {
			return {
				CustomBar: this.CustomBar,
				swiperHeight: this.windowHeight,
				richText: '',
			}
		},
		onLoad(e) {//预览打印
			_str = this;
			var srand = global.isLogin()[3];
			e.id != null ? 
			uni.request({
				url:'http://admin.ke-tao.top/uni-app/user-lists-detail.php',
				data:{
					id:e.id,
					srand:srand
				},
				method:'POST',
				header : {'content-type':'application/x-www-form-urlencoded'},
				success: (res) => {
					this.richText = res.data[0].content;
					console.log(this.richText)
				}
			})
			: this.richText = decodeURIComponent(e.rich);
		},
		methods: {
			// edit(){
			// 	uni.navigateTo({
			// 		url:
			// 	})
			// },
		}
	}
</script>

<style>

</style>
