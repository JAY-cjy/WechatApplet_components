<template>
	<view class="">
		<login v-if="!isCanUse || !isPhone" :imgs="imgs" :url="url" :isCanUse="isCanUse" :isPhone="isPhone" :width="'120rpx'" :height="'50rpx'"></login>
	</view>
</template>

<script>
	import login from "login.vue";

	export default {
		components: {
			login,
		},
		data() {
			return {
				imgs: this.imgs,
				url: this.url,
				memberId: uni.getStorageSync('memberId'),
				isCanUse: uni.getStorageSync('isCanUse'),
				isPhone: uni.getStorageSync('isPhone')
			}
		},
		onLoad() {

		},
		onShow() {
			this.memberId = uni.getStorageSync('memberId')
			this.isCanUse = uni.getStorageSync('isCanUse')
			this.isPhone = uni.getStorageSync('isPhone')
		},
		methods: {
			// 子组件
			changeIsCanUse(type) {
				if (type == 1) {
					this.isCanUse = true;
				} else {
					this.isPhone = true;
				}
			}
		},
	}
</script>

<style lang="scss">
</style>
