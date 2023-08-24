<template>
	<view>
		<map id="map" :style="contentStyle" 
			subkey="FGRBZ-GS266-44VSO-ER7OG-IW5S7-ANB47" scale="12" 
			:latitude="latitude" :longitude="longitude">
		</map>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				windowHeight: 0,
				contentStyle: '',
				latitude: 39.908823,
				longitude: 116.39747,
			}
		},
		methods: {
			
		},
		onShow: function() {
			let that = this;
			uni.$on('updateLocation', function(location) {
				if (location != null) {
					that.latitude = location.latitude;
					that.longitude = location.longitude;
				}
			});
		},
		onHide: function() {
			uni.$off('updateLocation');
			
		},
		onLoad:function(){
			let that=this
			let windowHeight=uni.getSystemInfoSync().windowHeight
			that.windowHeight=windowHeight
			that.contentStyle = `width: 750rpx;height:${that.windowHeight}px;`;
			
			let map = wx.createMapContext('map');
			map.addVisualLayer({
				layerId: '10f611245811',
				interval: 5,
				zIndex: 999,
				success: function(resp) {
					console.log(resp);
				},
				fail: function(error) {
					console.log(error);
				}
			})
		}
	}
</script>

<style></style>
