<template>
	<!-- 快看 -->
	<view>
		
		
		<web-view
			ref="myWebview2"
			id="myWebview2"
			data-id="myWebview2"
			:src="url"
			:webview-styles="webviewStyles"
		>
		</web-view>
		
		
		<!-- <cover-view
			class="webview-bt-box" 
			:style="{
				'width': '150rpx',
				'top': '0'
			}"
		>
			<cover-view class=" bt-item" @click="showChannelAction()">
				切换
			</cover-view>
		</cover-view> -->
	</view>
</template>

<script>
	export default {
		data() {
			return {
				webviewStyles: {
					progress: {
						color: '#FF3333'
					}
				},
				safeAreaInsetsTop: 0,
				url: "",
				// 准备解析的地址
				preParseUrl: null,
				activeChannel: 0,
				parseUrlChannelList: [
					{
						name: '方式1',
						url: 'https://jx.jsonplayer.com/player/?url='
					},
					{
						name: '方式2',
						url: 'https://2.08bk.com/?url='
					},
					{
						name: '方式3',
						url: 'https://jx.aidouer.net/?url='
					},
				]
			}
		},
		computed: {
			channelUrl(){
				let {parseUrlChannelList, activeChannel} = this;
				return parseUrlChannelList[activeChannel].url;
			},
		},
		onTabItemTap() {
			
		},
		onLoad(option) {
			let url = option.url;
			if (!url) {
				url = getApp().globalData.parseUrl;
			}
			if (url) {
				// this.url = this.getFullParseUrl(url);
				setTimeout(()=>{
					this.parseUrl(url);
				}, 1500)
			}
		},
		mounted() {
			uni.$on('onParseUrl', (data)=>{
				console.log("解析url事件", data);
				if (data) {
					this.parseUrl(data.url);
				}
			});
			
			let sysInfo = uni.getSystemInfoSync();
			console.log("sysInfo", sysInfo);
			this.safeAreaInsetsTop = sysInfo.safeAreaInsets.top + sysInfo.statusBarHeight + 44;
		},
		onShow() {
		},
		onNavigationBarButtonTap(e) {
			console.log("e");
			this.showChannelAction();
		},
		methods: {
			getWebview(){
				return this.$scope.$getAppWebview().children()[0];
			},
			getFullParseUrl(url){
				if (!url) {
					return;
				}
				url = decodeURIComponent(url);
				let {channelUrl} = this;
				let webview = this.getWebview();
				let parseUrl = channelUrl + url;
				return parseUrl;
			},
			/**
			 * 
			 */
			parseUrl(url){
				console.log("解析url", url);
				if (!url) {
					return;
				}
				let webview = this.getWebview();
				this.preParseUrl = url;
				let parseUrl = this.getFullParseUrl(url);
				console.log("最终解析url：", parseUrl);
				webview.loadURL(parseUrl);
			},
			/**
			 * 显示通道选项
			 */
			showChannelAction(){
				let names = this.parseUrlChannelList.map(item=>item.name);
				uni.showActionSheet({
					itemList: names,
					success: (res) => {
						console.log('选中了第' + (res.tapIndex + 1) + '个按钮');
						let index = res.tapIndex;
						this.activeChannel = index;
						this.parseUrl(this.preParseUrl);
					},
				});
			},
		},
	}
</script>

<style scoped>
	
	.webview-bt-box{
		z-index: 99999;
		width: 750rpx;
		height: 120rpx;
		position: fixed;
		left: 0;
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
</style>
