<script>
	export default {
		onLaunch: function() {
			//#ifdef APP-PLUS
			if (plus) {
				plus.navigator.closeSplashscreen();
			}
			// #endif
		}
	}
</script>

<style>
	/*每个页面公共css */
</style>
