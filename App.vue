<script>
  import Utils from './common/js/util.js'
	export default {
		onLaunch: function() {
			console.log('App Launch')
			Utils.request({
				url: '/app/init',
				success: () => {
					uni.setStorageSync('cfg',res.data.cfg)
				}
			})
		},
		onShow: function() {
			console.log('App Show')
		},
		onHide: function() {
			console.log('App Hide')
		},
    globalData: {
      utils : Utils,
			orders_filt: ''
    }
	}
</script>

<style>
	/*每个页面公共css */
  @import './app.css'
</style>
