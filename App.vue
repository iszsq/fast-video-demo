<script>
	export default {
		globalData: {
			// url地址
			parseUrl: '',
		},
		onLaunch: function() {
			console.log('App Launch');
			uni.showModal({
				title: '提示',
				content: '该app仅供学习交流，所有素材资源均来源于互联网！'
			});
		},
		onShow: function() {
			console.log('App Show')
		},
		onHide: function() {
			console.log('App Hide')
		}
	}
</script>

<style>
	/*每个页面公共css */
</style>
