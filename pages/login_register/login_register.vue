<template>
	<view>
		<cu-custom bg-color="bg-yellow" :isBack="true">
			<block slot='backText'>返回</block>
			<block slot='content'>登录</block>
		</cu-custom>
		<form>
			<view class="cu-form-group margin-top">
				<view class="title">邮箱</view>
				<input placeholder="请输入你的邮箱" :value="email" @input="changeEmail"></input>
			</view>
			<view class="cu-form-group margin-top">
				<view class="title">密码</view>
				<input placeholder="输入密码嗷" password=true :value="password" @input="changePassword"></input>
			</view>
			<view class="padding flex flex-direction" :hidden='loading'>
				<button class="cu-btn bg-gradual-green margin-tb-sm lg" @click="login">登录</button>
			</view>
			<view class="padding flex flex-direction" :hidden='!loading'>
				<button class="cu-btn bg-gradual-green margin-tb-sm lg"><text class="cuIcon-loading2 cuIconfont-spin"></text>登录</button>
			</view>
		</form>
	</view>
</template>

<script>
	import {
		host
	} from '../config/proxy.js'
	import {
		cuCustom
	} from '../../colorui/components/cu-custom/cu-custom.vue'
	export default {
		components: {
			cuCustom
		},
		data() {
			return {
				email: '',
				password: '',
				device: 'android',
				loading: false
			}
		},
		methods: {
			login: function() {
				this.loading = true
				uni.request({
					url: host + 'users/',
					method: 'POST',
					header: {
						'Content-Type': 'application/json'
					},
					data: {
						email: this.email,
						password: this.password,
						device: this.device
					},
					success: (res) => {
						if (res.statusCode == 200) {
							// 保存登录cookie
							uni.setStorageSync('loginCookie', res.header['Set-Cookie'])
							this.loading = false
							uni.navigateBack()
						} else if (res.statusCode == 404) {
							uni.showToast({
								title: '服务器未连接'
							})
							this.loading = false
						} else {
							console.log(res.data.errMsg)
							this.loading = false
						}
					}
				})
			},
			changeEmail: function(e) {
				this.email = e.detail.value
			},
			changePassword: function(e) {
				this.password = e.detail.value
			}
		}
	}
</script>

<style>

</style>
