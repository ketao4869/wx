<template>
	<view>
		<!-- 状态导航栏(原导航栏) -->
		<cu-custom bgColor="bg-gradual-blue" :isBack="true">
			<block slot="backText">返回</block>
			<block slot="content">编辑</block>
		</cu-custom>
		
		<!-- 顶部按钮组 -->
		<view class="margin-xs" style="display:flex;justify-content: space-between;">
			<view>
				<button class="cu-btn bg-blue shadow" @tap="elect" data-target="RadioModal">请选择分类：{{lab}}</button>
			</view>
			 <view> 
				<button class="cu-btn bg-gradual-blue round margin-right-lg" @tap="preview">预览</button>
				<button class="cu-btn bg-gradual-blue round" @tap="save">保存</button>
			 </view>
		</view>
		<!-- 选择分类区域，页面忽略他 -->
		<view class="cu-modal" :class="modalName == 'RadioModal' ? 'show' : ''" @tap="hideModal">
			<view class="cu-dialog"><!-- @tap.stop="" 可以让点击后暂停不自动消失 -->
				<radio-group class="block" @change="RadioChange">
					<view class="cu-list menu text-left">
						<view class="cu-item" v-for="(item,index) in label" :key="index" @click="labId(item.id)">
							<label class="flex justify-between align-center flex-sub">
								<view>{{item.title}}</view>
								<radio :value="item.title" class="blue"/>
							</label>
						</view>
					</view>
				</radio-group>
			</view>
		</view>
		<!-- 选择分类区域，页面忽略他 -->
		
		<!-- <view class="bg-white margin-tb-xs round">
			<view><text class="text-df">文章标题</text></view>
			<view>
				<input type="text" value="" placeholder="标题" />
			</view>
		</view> -->
		
		<!-- 编辑区 -->
		<view>
			<editor
			class="bg-white"
			id="editor"
			placeholder="开始输入~"
			show-img-resize
			show-img-size
			show-img-toolbar
			:read-only="readOnly"
			@statuschange="onStatusChange"
			@ready="onEditorReady">
			</editor>
		</view>
		<!-- 工具栏 -->
		<view id="main" @tap="format">
			<view class="a iconfont icon-jiacu" data-name="bold" data-label="加粗"></view>
			<view class="a iconfont icon-qingxie" data-name="italic" data-label="倾斜"></view>
			<view class="a iconfont icon-zuoduiqi" data-name="align" data-label="左对齐" data-value="left"></view>
			<view class="a iconfont icon-juzhong" data-name="align" data-label="居中" data-value="center"></view>
			<view class="a iconfont icon-youduiqi" data-name="align" data-label="右对齐" data-value="right"></view>
			<view class="a iconfont icon-tupian" @tap.stop="insertImage"></view><!-- .stop -->
			<view class="a iconfont icon-fengexian" @tap="infenge"></view>
			<view class="a iconfont icon-qingkong" @tap="cleform"></view>
		</view>
		
		
		
	</view>
</template>

<script>
var _imgURL;
var _bool;
	export default {
		data() {
			return {
				muiltImage:true,
				compressImage: false,
				imageUploader:'',
				readOnly: false,
				modalName: null,
				lab: '默认',
				lab_Id:'0',
				label:[
					{title:'标题1',id:1},
					{title:'标题2',id:2},
					{title:'标题3',id:3}
				]
			}
		},
		methods: {
			labId(e){//获取列表点击的ID,加载预览
				// console.log(e)
				this.lab_Id=e
			},
			elect(e) {
				this.modalName = e.currentTarget.dataset.target
			},
			hideModal(e) {
				this.modalName = null
			},
			RadioChange(e) {
				this.lab = e.detail.value
			},
			preview(){//预览
				this.editorCtx.getContents({
					success: function(res) {
						console.log('预览内容:', res.html)
						uni.navigateTo({
							url: `/pages/rests/detail?rich=${encodeURIComponent(res.html)}`,
						});
					}
				});
			},
			// 编辑器初始化完成时触发（自动执行）
			onEditorReady() {
				uni.createSelectorQuery().select('#editor').context((res) => {
					this.editorCtx = res.context
				}).exec()
			},
			format(e) {//点击工具栏
				let { name, value, label } = e.target.dataset;
				if (!name) return;
				this._format(name, value, label);
			},
			_format(name, value, label) {//传值
				this.editorCtx.format(name, value);
				this.toast(label);//赋值
			},
			toast(label) {//提示点击的消息
				uni.showToast({
					duration: 600,
					icon: 'none',
					title: label
				});
			},
			onStatusChange(e) {//改变样式
				const formats = e.detail;
				this.formats = formats;
					console.log(formats)
			},
			infenge() {
				this.editorCtx.insertDivider({
					success: res => {
						this.toast('插入分割线');
					}
				});
			},
			cleform(){
				this.editorCtx.removeFormat();
				this.toast('清除格式');
			},
			insertImage() {
				uni.chooseImage({
					success: res => {
						_imgURL = res;
						res.tempFilePaths.map(url => {
							console.log(url)
							this.editorCtx.insertImage({
								src:url,
								alt: '图片加载失败'
							});
						});
					}
				});
			},
			save() {
				console.log('保存');
				console.log(this.lab_Id)
				this.editorCtx.getContents({//获取编辑器内容
					success: res => {
						console.log(res.html);
						console.log(_imgURL);
						if(_imgURL != null){//上传图片
							_imgURL.tempFilePaths.map(path => {
								uni.uploadFile({
									url:'http://admin.ke-tao.top/uni-app/upload-img.php',
									filePath: path,
									name: 'file',
									success: (res) => {
										if(res.data == 0){
											uni.showToast({
												title: '图片上传成功',
												image: '../../static/icon/success.png'
											});
											// var a='ok';
										}else if(res.data == 2){
											uni.showToast({
												title: '图片上传失败',
												image: '../../static/icon/error.png'
											});
											return;
										}else{
											uni.showToast({
												title: '非法文件格式',
												image: '../../static/icon/error.png'
											});
											return;
										}
									},
								});
							});
						}
						var srand = global.isLogin()[3];
						console.log(srand);
						uni.request({
							url:'http://admin.ke-tao.top/uni-app/upload-html.php',
							data:{
								data:res.html,
								srand:srand
							},
							method:'POST',
							header : {'content-type':'application/x-www-form-urlencoded'},
							success: (res) => {
								console.log(res.data);
								// console.log(a)
							}
						});
					}
				});
			},
		}
	}
</script>

<style>
#main{margin-top: 10px; width: 100%;height: 150px;border: 1px solid #c3c3c3;display: flex;justify-content: space-between;flex-wrap:wrap;}
.a{width:70rpx;height:70rpx;margin:10rpx;}
</style>
