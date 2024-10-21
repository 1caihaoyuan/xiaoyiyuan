<template>
	<view>
		<navbar title-text="" :isHeight="false" :isWhite="true" background="none" @navBarAttached="onNavBarAttached" />
		<view style="position: relative;">
			<image
				:src="hospital.avatar_url"
				mode="aspectFill"
				style="filter: blur(50rpx) brightness(0.8); display: block; width: 100%; height: 550rpx; overflow: hidden"
			></image>
			<view :style="'position:absolute;top:' + navBarHeight + 'rpx;padding-top:65rpx;overflow:hidden;width:100%;'">
				<view class="hospital-hd">
					<view class="weui-cell weui-cell_access" hover-class="weui-cell_active" @tap="showShareModal">
						<view class="weui-cell__hd">
							<image
								:src="hospital.avatar_url"
								mode="aspectFill"
								style="position: absolute; top: -65rpx; display: block; width: 150rpx; height: 135rpx; border-radius: 10rpx; overflow: hidden"
							></image>
						</view>
						<view class="weui-cell__bd" style="padding-left: 170rpx">
							<view style="position: absolute; top: -65rpx">
								<text style="font-size: 36rpx; color: #ffffff; font-weight: bold">{{ hospital.name }}</text>
							</view>
							<view>
								<text class="hosp-rank">{{ hospital.rank }}</text>
								<text class="hosp-label">{{ hospital.label }}</text>
							</view>
						</view>
						<view class="weui-cell__ft weui-cell__ft_in-access"><text class="f4">转发</text></view>
					</view>
					<view class="weui-cell weui-cell_access" hover-class="weui-cell_active" @tap="toMap">
						<view class="weui-cell__hd">
							<image
								src="/static/resource/images/ic_address.png"
								mode="aspectFill"
								style="margin-right: 10rpx; display: block; width: 40rpx; height: 40rpx"
							></image>
						</view>
						<view class="weui-cell__bd">
							<view>
								<text style="font-size: 24rpx">{{ hospital.city }}{{ hospital.district }}{{ hospital.address }}</text>
							</view>
						</view>
						<view class="weui-cell__ft weui-cell__ft_in-access"><text class="f4">导航</text></view>
					</view>
				</view>
				<view class="hospital-bd">
					<view class="weui-cells serv-list">
						<view class="weui-cell serv-item">
							<view class="weui-cell__bd">
								<view style="padding-top: 10rpx"><text class="serv-name">在线预约您需要的服务</text></view>
							</view>
							<view class="weui-cell__ft"></view>
						</view>
						<!-- tap 移动端触摸 -->
						<view class="weui-cell serv-item" @tap="toService" :data-svid="item.id" v-for="(item, index) in services" :key="index">
							<block v-if="item.use_switch == 1">
								<view class="weui-cell__hd">
									<image class="serv-logo" :src="item.logo_image ? item.logo_image_url : '../../resource/images/avatar.jpg'" mode="aspectFill" />
								</view>
								
								<view class="weui-cell__bd">
									<view>
										<text class="serv-name">{{ item.name }}</text>
									</view>
									<view class="serv-line serv-intro">{{ item.intro }}</view>
									<view class="serv-line">
										<text class="serv-price">{{ item.price }}</text>
										<text class="serv-unit">元/次</text>
									</view>
								</view>
								
								<view class="weui-cell__ft">
									<button class="btn1m">预约</button>
								</view>
							</block>
						</view>
					</view>
				</view>
			</view>
		</view>
		<share :shareModal="clone_shareModal"></share>
	</view>
</template>

<script setup>
	const app = getApp()
	import { ref } from 'vue'
	import { onLoad } from '@dcloudio/uni-app'
	const navBarHeight = ref('')
	// 服务列表
	const services = ref('')
	const onNavBarAttached = (e) => {
		navBarHeight.value = e.detail.navBarHeight
	}
	// 转发
	const showShareModal = () => {
		clone_shareModal.value = true
	}
	// 定义医院详情的数据
	const hospital = ref({})
	onLoad((params) => {
		app.globalData.utils.request({
			url: '/Hospital/index',
			data: {
				hid: params.hid
			},
			success: (res) => {
				console.log(res)
				hospital.value = res.data.hospital
				services.value = res.data.services
			}
		})
	})
	// 分享功能是否打开
	const clone_shareModal = ref(false)
	// 打开导航的功能
	const toMap = () => {
		const point = bMapTransQQMap(hospital.value.lng, hospital.value.lat)
		const { qmap_key:key } = uni.getStorageSync('cfg')
		const referer = app.globalData.name
		const endPoint = JSON.stringify({
			name: hospital.value.name,
			latitude: point.lat,
			longitude: point.lng
		})
		uni.navigateTo({
			url:'plugin://routePlan/index?key=' + key + '&referer=' + referer + '&endPoint=' + endPoint + '&navigation=1'
			
		})
	}
	const bMapTransQQMap = (lng, lat) => {
		let x_pi = (3.141592653589793 * 3000) / 180
		let x = lng - 0.0065
		let y = lat - 0.006
		let z =Math.sqrt(x * x + y * y) - 0.0002 * Math.sin(y * x_pi)
		let theta = Math.atan2(y, x) - 0.000003 * Math.cos(x * x_pi)
		let lngs = z * Math.cos(theta)
		let lats = z * Math.sin(theta)
		return{
			lng: lngs,
			lat: lats
		}
	}
	
	const toService = (e) => {
		uni.navigateTo({
			url:'../service/index?hid=' + hospital.value.id + '&svid=' + e.currentTarget.dataset.svid
		})
	}
</script>

<style>
page {
    background: #f4f4f4;
}

.hospital-hd {
    margin: 20rpx 20rpx 0 20rpx;
    background: #ffffff;
    border-radius: 10rpx;
}
.hospital-hd .weui-cell {
    padding: 20rpx;
}

.hospital-bd {
    margin: 20rpx 20rpx 0 20rpx;
    background: #ffffff;
    border-radius: 10rpx;
}
.hospital-bd .weui-cells {
    margin-top: 0;
}
.hospital-bd .weui-cell {
    padding: 20rpx;
}

.hospital-ft {
    margin: 20rpx 20rpx 0 20rpx;
    background: #ffffff;
    border-radius: 10rpx;
}
.hospital-ft .weui-cell {
    padding: 20rpx;
}

.hosp-rank {
    font-size: 26rpx;
    font-weight: bold;
    color: #0bb585;
    margin-right: 15rpx;
}
.hosp-label {
    font-size: 26rpx;
    font-weight: bold;
    color: #0ca7ae;
    margin-right: 15rpx;
}

.serv-list {
    margin: 0;
    background: none;
}
.serv-list::before {
    display: none;
}
.serv-list::after {
    display: none;
}

.serv-item {
    padding: 20rpx;
    background: #ffffff;
    border-radius: 10rpx;
    overflow: hidden;
}
.serv-item::before {
    display: none;
}
.serv-item::after {
    display: none;
}
.serv-name {
    font-weight: bold;
    font-size: 34rpx;
}
.serv-logo {
    display: block;
    width: 150rpx;
    height: 150rpx;
    border-radius: 10rpx;
    overflow: hidden;
    margin-right: 20rpx;
}
.serv-line {
    margin-top: 8rpx;
}
.serv-line text {
    font-size: 26rpx;
}
.serv-rank {
    font-weight: bold;
    color: #0bb585;
    margin-right: 15rpx;
}
.serv-label {
    font-weight: bold;
    color: #0ca7ae;
    margin-right: 15rpx;
}
.serv-intro {
    font-size: 26rpx;
    color: #999999;
    width: 350rpx;
    overflow: hidden;
    white-space: nowrap;
    text-overflow: ellipsis;
}
.serv-price {
    font-size: 34rpx !important;
    font-weight: bold;
    color: #0bb684;
}
.serv-unit {
    color: #0bb684;
}

</style>
