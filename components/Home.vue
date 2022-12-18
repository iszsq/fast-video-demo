<template>
	<!-- 首页组件、搜索框、网站宫格。。。 -->
	<view class="home-wrapper">
		<view class="logo-box">
			<uni-icons type="search" size="80"></uni-icons>
		</view>
		
		<!-- 搜索框 -->
		<view class="search-box">
			<uni-search-bar 
				:value="url"
				class="search-bar"
				placeholder="输入网址..." bgColor="#EEEEEE" @confirm="search" 
			/>
		</view>
		
		<!-- 收藏网址列表 -->
		<view class="website-list-box">
			<uni-grid
				:column="3" :showBorder="false"  :square="false"
				@change="onClickWebsiteItem"
			>
				<uni-grid-item
					v-for="(item,index) in websiteList"
					:index="index"
				>
					<view class="website-item">
						<view class="icon">
							<uni-icons type="paperplane-filled" size="22"></uni-icons>
						</view>
						<view class="title">
							<text>{{item.title}}</text>
						</view>
					</view>
				</uni-grid-item>
			</uni-grid>
		</view>
	</view>
</template>

<script>
	export default {
		props: {
			url: String
		},
		data() {
			return {
				// 网站列表
				websiteList: [
					{
						title: '爱奇艺',
						url: 'https://m.iqiyi.com',
					},
					{
						title: '腾讯视频',
						url: 'https://m.v.qq.com',
					},
					{
						title: '优酷',
						url: 'https://www.youku.com',
					},
					{
						title: '芒果TV',
						url: 'https://m.mgtv.com',
					},
				]
			}
		},
		methods: {
			/**
			 * 输入网址后
			 * @param {Object} value
			 */
			search(data){
				let value = data.value;
				console.log("url: ", value);
				this.$emit("openUrl", {
					url: value
				});
			},
			/**
			 * 点击收藏网址项
			 * @param {Object} e
			 */
			onClickWebsiteItem(e){
				console.log("点击收藏网址项: ", e);
				let index = e.detail.index;
				let item = this.websiteList[index];
				console.log("网址项: ", item);
				this.$emit("openUrl", {
					url: item.url
				});
			},
		},
	}
</script>

<style scoped>
	.home-wrapper {
		width: 750rpx;
		flex: 1;
	}
	
	.logo-box{
		text-align: center;
		margin: 100rpx 0;
	}
	
	.search-box {
	}
	.search-box .search-bar{
	}
	
	.website-list-box{
		margin-top: 36rpx;
	}
	.website-list-box .website-item{
		padding: 24rpx;
		margin-bottom: 24rpx;
		justify-content: center;
		align-items: center;
	}
	.website-list-box .website-item .icon{
		text-align: center;
		margin-bottom: 12rpx;
	}
	.website-list-box .website-item .title{
		text-align: center;
	}
</style>