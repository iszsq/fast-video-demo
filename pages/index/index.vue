<template>
	<!-- 首页 -->
	<view class="content" :style="{'height': pageHeight}">
		<!-- 首页模块：搜索、收藏网址... -->
		<Home 
			v-if="showMode === 'home'"
			:url="url"
			@openUrl="openUrl"
		></Home>
		
		<!-- webview浏览模块 -->
		<view class="webview-box" v-if="showMode === 'webview'">
			<!-- <view class="nav-bar-box">
				<uni-nav-bar
					shadow left-icon="left" right-icon="home" title="" 
					statusBar
					@clickRight="navClickRight"
				/>
			</view> -->
			<view class="web-view-contain">				
				<web-view
					ref="myWebview"
					id="myWebview"
					data-id="myWebview"
					:src="url"
					:fullscreen="false"
					@onPostMessage="onWvMeesage"
					:webview-styles="webviewStyles"
				>
					
				</web-view>
			</view>
			
		</view>
		
		<cover-view
			class="webview-bt-box" 
			:style="{
				'width': isCloseWbBts === true ? '100rpx' : '750rpx',
				'bottom': safeAreaInsetsBottom ? (80 + safeAreaInsetsBottom) + 'px' : '70px'
			}"
		>
			<cover-view class="return bt-item" v-if="isCloseWbBts" @click="onClickCloseWbBts(false)">
				展开
			</cover-view>
			
			<cover-view class="return bt-item" v-if="!isCloseWbBts" @click="navClickLeft()">
				返回
			</cover-view>
			<cover-view class=" bt-item" v-if="!isCloseWbBts" @click="onClickForward()">
				前进
			</cover-view>
			<cover-view class=" bt-item" v-if="!isCloseWbBts" @click="navClickRight()">
				首页
			</cover-view>
			<cover-view class=" bt-item" v-if="!isCloseWbBts" @click="onClickCloseWbBts(true)">
				收起
			</cover-view>
			<cover-view class=" bt-item"v-if="!isCloseWbBts" @click="toFastVideo()">
				快看
			</cover-view>
		</cover-view>
		
		<!-- <view class="float-bt-box">
			<uni-fab
				style="z-index: 99999;"
				ref="fab"  :content="fabButtons" horizontal="left" :vertical="fabVertical"
				direction="horizontal" @fabClick="onFabClick" 
				@trigger="onFabTrigger"
			/>
		</view> -->
		
		<!-- <view style="position: fixed;bottom: 0;left: 0;padding: 22rpx;background-color: #f0f0f0;">fdfdf</view> -->
		
	</view>
</template>

<script>
	import Home from '@/components/Home';

	export default {
		components: {
			Home
		},
		data() {
			return {
				webviewStyles: {
					progress: {
						color: '#FF3333'
					}
				},
				// 撑满高度
				pageHeight: "100%",
				// 显示模块：首页home，webview：网站浏览器
				showMode: 'home',
				// 当前访问的url
				url: null,
				
				// 浮动按钮配置
				fabButtons: [
					{
						text: '返回',
						active: false,
						method: 'navClickLeft'
					},
					{
						text: '首页',
						active: false,
						method: 'navClickRight'
					},
					{
						text: '切换位置',
						active: false,
						method: 'onFabClickChangePosition'
					},
					{
						text: '快看',
						active: false,
						method: 'toFastVideo'
					},
				],
				fabVertical: "bottom",
				
				isCloseWbBts: false,
				safeAreaInsetsBottom: 70,
			}
		},
		onLoad() {
			console.log("onload")
		},
		onShow() {
			console.log("onShow")
		},
		onBackPress() {
			console.log("点击返回");
			let webview = this.getWebview();
			console.log("webview ", webview);
			console.log("can back", webview.canBack(e=>{console.log("e", e)}));
			console.log("url", webview.getURL());
			console.log("evalJSSync", webview.evalJSSync('location.href'));
			// console.log("evalJS", webview.evalJS('alert(location.href)'));
			
			// console.log("back", webview.back());
			this.navClickLeft();
			return true;
		},
		onNavigationBarButtonTap(e) {
			console.log("e", e);
			if (e.text === '首页') {
				this.navClickRight();
			} else {
				this.toFastVideo();
			}
		},
		mounted() {
			let sysInfo = uni.getSystemInfoSync();
			console.log("sysInfo", sysInfo);
			let height = sysInfo.safeArea.height + sysInfo.statusBarHeight;
			this.pageHeight = height + 'px';
			this.safeAreaInsetsBottom = sysInfo.safeAreaInsets.bottom;
			
			// setInterval(()=>{
			// 	this.toFastVideo();
			// }, 10000);
		},
		onTabItemTap(e) {
			console.log("onTabItemTap", e);
			
		},
		methods: {
			/**
			 * 打开网址
			 * @param {Object} data
			 */
			openUrl(data){
				let url = data.url;
				if (!url) {
					alert("请输入网址")
					return;
				}
				this.url = url;
				this.showMode = "webview";
				// plus.webview.open(url, "myWebview", {
				// 	top: '0',
				// 	bottom: "100px",
				// })
			},
			getWebview(){
				// return this.$refs.myWebview;
				// return this.$scope.page.$getAppWebview();
				console.log("plus.android.currentWebview()", plus.webview.getWebviewById("myWebview"));
				return this.$scope.$getAppWebview().children()[0]
				let pages = getCurrentPages();
				let page = pages[pages.length - 1]; 
				let webview = page.$getAppWebview();
				return webview;
			},
			/**
			 * 点击右边首页图标
			 */
			navClickRight(){
				this.url = null;
				this.showMode = "home";
				uni.setNavigationBarTitle({
					title: 'Hi'
				})
			},
			/**
			 * 点击左边返回
			 */
			navClickLeft(){
				let webview = this.getWebview();
				webview.back();
			},
			onClickForward(){
				let webveiw = this.getWebview();
				webveiw.forward();
			},
			onWvMeesage(e){
				console.log("收到事件", e);
			},
			/**
			 * 浮动按钮点击事件
			 */
			onFabClick(e){
				console.log("浮动按钮点击事件：", e);
			},
			/**
			 * 浮动按钮展开项点击事件
			 */
			onFabTrigger(e){
				console.log("浮动按钮展开项点击事件：", e);
				let item = e.item;
				let method = this[item.method];
				if (typeof method === 'function') {
					method(e);
				}
			},
			/**
			 * 浮动按钮切换位置
			 */
			onFabClickChangePosition(e){
				console.log("浮动按钮切换位置")
				let res = this.fabVertical === 'bottom' ? 'top' : 'bottom';
				this.fabVertical = res;
			},
			/**
			 * 点击快看
			 */
			toFastVideo(e){
				let webview = this.getWebview();
				if (!webview) {
					uni.showToast({
						icon:'none',
						title: "请先访问到视频播放页面"
					});
					return;
				}
				console.log("webview ", webview);
				let url = webview.evalJSSync('location.href');
				url = url.replace(/\"/g, "");
				console.log("url" +  url);
				getApp().globalData.parseUrl = url;
				uni.switchTab({
					url: '/pages/fast-video/fast-video',
				});
				uni.$emit('onParseUrl', {
					url: url
				})
			},
			onClickCloseWbBts(value){
				console.log("点击收起")
				this.isCloseWbBts = value;
			},
		}
	}
</script>

<style>
	
	
	.content {
		width: 750rpx;
		/* top: 0; */
		/* left: 0; */
		position: absolute;
		flex: 1;
	}
	
	.webview-box {
		width: 750rpx;
		height: 100%;
		flex: 1;
		display: flex;
		flex-direction: column;
		flex-wrap: nowrap;
	}
	
	.webview-box .web-view-contain {
		width: 750rpx;
		height: 0;
		flex: 1;
		position: relative;
		background-color: aliceblue;
	}
	
	.float-bt-box{
		z-index: 99999;
		
	}


	.webview-bt-box{
		z-index: 99999;
		width: 750rpx;
		height: 120rpx;
		position: fixed;
		left: 0;
		bottom: 70px;
		border-radius: 10px;
		box-sizing: border-box;
		color: #ffffff;
		display: flex;
		flex-direction: row;
		flex-wrap: nowrap;
		overflow: hidden;
		padding: 12rpx;
	}
	.webview-bt-box .bt-item{
		width: 0;
		height: 100%;
		z-index: 99999;
		color: #ffffff;
		flex: 1;
		text-align: center;
		display: flex;
		align-items: center;
		line-height: 80rpx;
		background-color: rgba(0, 0, 0, 0.6);
		justify-content: center;
		/* box-shadow: 1px 10px 2px rgba(0,0, 0, .2); */
	}
	.webview-bt-box .return{
		z-index: 99999;
	}
</style>
