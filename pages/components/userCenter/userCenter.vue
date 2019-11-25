<template name='userCenter'>
	<view>
		<view class="bg-img flex align-center userBG">
			<view class="flex-item" style="margin: auto;flex-direction: column;" @click="toLogin" v-if="!isLogin">
				<view class="flex-item flex-item-V">
					<view class="cu-avatar xl round" style="background-image: url('https://utime-uniapp-oss.oss-cn-shenzhen.aliyuncs.com/static/user/avatar.png'); margin: auto;">
					</view>
				</view>
				<view class="flex-item flex-item-V">
					<text>你还没登录呢</text>
				</view>
			</view>
			<view class="flex-item" style="margin: auto; flex-direction: column;" v-if="isLogin">
				<view class="flex-item flex-item-V">
					<text>欢迎来到祖安</text>
				</view>
			</view>
		</view>
		<uni-grid :column="4" :showBorder="false">
			<uni-grid-item>
				<view class="grid-item-box">
					<text class="text">未支付</text>
					<view class="grid-dot" v-if="unpaid_number!='0'&&isLogin">
						<uni-badge :text="unpaid_number + ' '" type='primary'></uni-badge>
					</view>
				</view>
			</uni-grid-item>
			<uni-grid-item>
				<view class="grid-item-box">
					<text class="text">待接单</text>
					<view class="grid-dot" v-if="waiting_number!='0'&&isLogin">
						<uni-badge :text="waiting_number + ' '" type='primary'></uni-badge>
					</view>
				</view>
			</uni-grid-item>
			<uni-grid-item>
				<view class="grid-item-box">
					<text class="text">待完成</text>
					<view class="grid-dot" v-if="waiting_for_finish_number!='0'&&isLogin">
						<uni-badge :text="waiting_for_finish_number + ' '" type='primary'></uni-badge>
					</view>
				</view>
			</uni-grid-item>
			<uni-grid-item>
				<view class="grid-item-box">
					<text class="text">待评价</text>
					<view class="grid-dot" v-if="unrated_number!='0'&&isLogin">
						<uni-badge :text="unrated_number + ' '" type='primary'></uni-badge>
					</view>
				</view>
			</uni-grid-item>
		</uni-grid>
		<view class="padding flex flex-direction" v-if="isLogin">
			<button class="cu-btn bg-red margin-tb-sm lg" @click="logout">登出</button>
		</view>
	</view>
</template>

<script>
	import {
		host
	} from '../../config/proxy.js'
	import uniGrid from '@dcloudio/uni-ui/lib/uni-grid/uni-grid.vue'
	import uniGridItem from '@dcloudio/uni-ui/lib/uni-grid-item/uni-grid-item.vue'
	import uniBadge from '@dcloudio/uni-ui/lib/uni-badge/uni-badge.vue'
	export default {
		name: 'userCenter',
		components: {
			uniGrid,
			uniGridItem,
			uniBadge
		},
		data() {
			return {
				isLogin: false,
				nickname: '',
				email_active: false,
				es_check: false,
				unpaid_number: '',
				waiting_for_finish_number: '',
				waiting_number: '',
				unrated_number: ''
			}
		},
		methods: {
			checkLogin: function() {
				uni.request({
					url: host + 'users/me/',
					header: {
						'Cookie': uni.getStorageSync('loginCookie')
					},
					success: (res) => {
						if (res.statusCode == 200) {
							this.isLogin = true
							this.nickname = res.data.myself.nickname
							this.email_active = res.data.myself.email_active
							this.es_check = res.data.myself.es_check
							this.unpaid_number = res.data.tailwind_number.unpaid_number
							this.waiting_for_finish_number = res.data.tailwind_number.waiting_for_finish_number
							this.waiting_number = res.data.tailwind_number.waiting_number
							this.unrated_number = res.data.tailwind_number.unrated_number
						} else if (res.statusCode == 404) {
							console.warn('error')
						} else {
							this.isLogin = false
							console.log(res.data.errMsg)
						}
					}
				})
			},
			toLogin: function() {
				uni.navigateTo({
					url: '/pages/login_register/login_register'
				})
			},
			logout: function() {
				uni.request({
					url: host + 'users/',
					method: 'DELETE',
					success: (res) => {
						if (res.statusCode == 200) {
							console.log('登出成功')
							uni.removeStorageSync('loginCookie')
						} else {
							console.log(res.data.errMsg)
						}
					}
				})
			}
		},
		onReady() {
			this.checkLogin()
		}
	}
</script>

<style>
	.userBG {
		background-image: url('https://utime-uniapp-oss.oss-cn-shenzhen.aliyuncs.com/static/user/userbg.jpeg');
		height: 414upx;
	}

	.text {
		font-size: 26rpx;
		margin-top: 10rpx;
	}

	.grid-item-box {
		flex: 1;
		/* position: relative;
	*/
		/* #ifndef APP-NVUE */
		display: flex;
		/* #endif */
		flex-direction: column;
		align-items: center;
		justify-content: center;
		padding: 15px 0;
	}

	.grid-dot {
		margin-top: -5rpx;
		position: absolute;
		top: 5px;
		right: 15px;
	}
</style>
