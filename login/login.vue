<template>
	<view :style="'position: relative;width:' + width + ';height:' + height + ';'">
		<button v-if="!isCanUse" :style="'width:' + width + ';height:' + height + ';'" class="userInfo" open-type="getUserInfo"
		 @getuserinfo="onGotUserInfo"></button>

		<button v-if="!isPhone" :style="'width:' + width + ';height:' + height + ';'" class="userInfo phone" open-type="getPhoneNumber"
		 @getphonenumber="decryptPhoneNumber"></button>
	</view>
</template>

<script>
	export default {
		//属性
		props: {
			imgs: '',
			url: '',
			isCanUse: '',
			isPhone: '',
			width: '',
			height: '',
		},
		data() {
			return {}
		},
		onLoad() {},
		methods: {
			onGotUserInfo(e) {
				console.log(e.detail.userInfo);
				if (e.detail.userInfo) {
					uni.setStorageSync('isCanUse', true);
					this.$parent.changeIsCanUse(1);
				}
			},

			decryptPhoneNumber(e) {
				var ivObj = e.detail.iv;
				var telObj = e.detail.encryptedData;
				var codeObj = "";
				var that = this;

				let sessionKey = uni.getStorageSync('session_key');
				let openId = uni.getStorageSync('openid');
				
				// console.log(e.detail.iv, e.detail.encryptedData)
				// if (e.detail.errMsg == 'getPhoneNumber:user deny') { //用户点击拒绝
				// } else { //允许授权执行跳转
				// }
				
				// 是否授权
				if (e.detail.errMsg == 'getPhoneNumber:ok') {
					uni.request({
						url: that.url + '/mobile/member/decryptUserInfo',
						data: {
							encryptedData: e.detail.encryptedData,
							sessionKey: sessionKey,
							iv: e.detail.iv
						},
						success: function(res) {
							console.log(res)
							if (!res.data.code) {
								return;
							}

							let phone = JSON.parse(res.data.msg).phoneNumber
							uni.request({
								url: that.url + '/mobile/member/mallMobileLogin',
								data: {
									openid: openId,
									phone: phone,
								},
								success: function(res_2) {
									console.log(res_2)
									uni.setStorageSync('phone', phone);
									uni.setStorageSync('memberId', res_2.data.data.memberId);
									uni.setStorageSync('isPhone', true);
									that.$parent.changeIsCanUse(2);
								}
							})

						}
					})
				} else {

				}
			},
		},
	}
</script>

<style lang="scss">
	.image {
		height: 40rpx;
		width: 40rpx;
	}

	.userInfo {
		width: 40rpx;
		height: 40rpx;
		position: absolute;
		padding: 0;
		box-sizing: border-box;
		top: 0;
		left: 0;
		opacity: 0;
		z-index: 99;
	}

	.phone {
		z-index: 98;
	}
</style>
