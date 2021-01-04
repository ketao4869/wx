<template>
	<view :style="{height:swiperHeight+'px'}">
		<!-- 状态导航栏(原导航栏) -->
		<cu-custom bgColor="bg-gradual-blue">
			<block slot="content">列表</block>
		</cu-custom>
		<view style="width: 100%;" :style="{height:swiperHeight-CustomBar+'px'}">
			<!-- 列表的导航栏 -->
			<view class="bg-white nav">
				<view class="flex text-center">
					<view class="cu-item flex-sub" :class="index==TabCur?'text-blue cur':''" 
					v-for="(tabBar,index) in tabBars" :key="index" @tap="tabSelect" :id="tabBar.id" :data-id="index">
						{{tabBar.name}}
					</view>
				</view>
			</view>
			<!-- 可移动视图区域 -->
			<movable-area :style="{height:movableHeight}">
				<!-- 滑动区域 -->
				<swiper style="height:100%;" :current="TabCur" @change="tabChange">
					<swiper-item v-for="(tabBars,index) in tabBars" :key="index" >
						<scroll-view scroll-y style="width: 100%; height: 100%;">
							<view class="padding-xs ">
								
								<view class="cu-list menu card-menu a">
									<view v-for="(list,index) in lists" :key="index" :id="list.id" class="cu-item arrow" @click="goDetail(list.id)">
										<view class="content">
											<text class="cuIcon-tagfill text-red  margin-right-xs"></text>
											<text class="text-grey">{{list.title}}</text>
										</view>
										<view class="action">
											<view class="cu-tag round bg-mauve light">{{list.label}}</view>
										</view>
									</view>
								</view>
								
							</view>
						</scroll-view>
					</swiper-item>
					<swiper-item>
						<scroll-view scroll-y style="width: 100%; height: 100%;">
							<view class="margin-left-sm margin-right-sm padding-left-sm padding-right-sm ">
								没有页面了
							</view>
						</scroll-view>
					</swiper-item>
				</swiper>
				<!-- 小圆圈（添加页面） -->
				<movable-view direction="all" :x="x" :y="y">
					<view class="box1" @click="newnode">
						<view class="one"></view>
						<view class="two"></view>
					</view>
				</movable-view>
			</movable-area>
		</view>
	</view>
</template>

<script>
var _str;
	export default {
		data() {
			return {
				// this.是App.vue中定义了的
				CustomBar: this.CustomBar,
				swiperHeight: this.windowHeight,
				x:this.x,
				y:this.y,
				TabCur: 0,
				scrollLeft: 0,
				menuArrow: true,
				menuCard: true,
				tabBars: [//这个居中对齐样式的导航栏不要超过6个标题
					{ name: '备忘录', id: 'beiwanglu' },
					{ name: '笔记', id: 'biji' }
				],
				lists : []
			}
		},
		computed: {
			movableHeight(){
				return this.swiperHeight-this.CustomBar-40+'px';
			}
        },
		onShow() {
			_str = this;
			var srand = global.isLogin()[3];
			uni.request({
				url:'http://admin.ke-tao.top/uni-app/user-lists-info.php',
				method:'POST',
				header : {'content-type':'application/x-www-form-urlencoded'},
				data:{srand:srand},
				success: (res) => {
					if(res.data == 1){
						/* return */ console.log('查找openid失败');
						
					}else if(res.data == 2){
						/* return */ console.log('查找数据失败');
					}else{
						console.log(res)//查找到的数据返回给lists数组
						_str.lists = res.data;
					}
				}
			});
		},
		methods: {
			newnode(){
				var res = global.isLogin();
				if (!res) {
					uni.showModal({
						// 弹出可以确定取消的模态窗
					    title: '提示',
					    content: '你未登录，请登录后再试',
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
					uni.navigateTo({
						url: '/pages/rests/edit'
					});
				}
			},
			tabChange(e){//滑动切换页面内容
				this.TabCur = e.detail.current;
			},
			tabSelect(e) {//点击导航栏切换页面内容
				this.TabCur = e.currentTarget.dataset.id;
				this.scrollLeft = (e.currentTarget.dataset.id - 1) * 60;
			},
			goDetail(e){//传的是lab的id
				console.log(e);
				uni.navigateTo({
					url: `/pages/rests/detail?id=${encodeURIComponent(e)}`
				});
			}
		}
	}
</script>

<style>
movable-view {height: 50px;width: 50px;}
movable-area {height:100%; width: 100%;}

.box1 {width: 50px;height: 50px;background-color: #1E90FF;border-radius: 50%}
.one{width:30px; height:6px; background-color:#ffffff; position:fixed; right:10px; bottom:22px;}
.two{width:6px; height:30px; background-color:#ffffff; position:fixed; right:22px; bottom:10px;}
</style>
