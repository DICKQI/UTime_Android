<template>
	<view>
		<view v-if="pageCur=='1'">
			<!-- 主页 -->
			<view :style='"width: 100%; background: #fbbd08; position: fixed; top: 0; z-index: 1;" + "height: " + statusBar + "px;"'></view>
			<view :style='"position: fixed; z-index: 1;" + "top: " + statusBar + "px;" + "height: " + custom + "px"'>
				<uni-nav-bar background-color='#fbbd08' :fixed='true' :border="border" right-icon='scan' @click-right='clickScan'
				 left-icon='bars' :status-bar='false'>
					<view class="input-view">
						<input confirm-type="search" class="nav-bar-input" type="text" placeholder="可以输入地点搜索噢" @confirm="search" @focus="clickSearch"
						 @blur="blurSearch" :value="searchValue" @input="changeSearchValue" />
						<view v-if="clearShow" style="right: 12vh; position: fixed;">
							<uni-icons color="#999999" size="26" type="clear" @click='clearSeachValue' />
						</view>
					</view>
				</uni-nav-bar>
			</view>
			<view :style='"margin-bottom: 150rpx; margin-top: " + (custom + 10) + "px"'>
				<view v-for="(item, index) in tailwindRequestList" :key='index'>
					<uni-card thumbnail='/static/logo.png' :is-shadow="true" mode='title' :title='item.initiator.user' @click='test' data-reid=item.initiator.id>
						<view style="position: relative;">
							<image src="/static/index/default.jpeg" mode="aspectFill"></image>
						</view>
						<view><text style="color: gray;">{{"截止时间：" + item.endTime}}</text></view>
						<view><text>{{item.taskContent}}</text></view>
					</uni-card>
				</view>
			</view>
		</view>
		<view v-if="pageCur=='2'">
			<!-- 个人页面 -->
			<userCenter></userCenter>
		</view>
		<!-- foot-bar -->
		<view class="cu-bar tabbar bg-white shadow foot">
			<view class="action" @click="navChange" data-cur='1'>
				<view class="cuIcon-cu-image">
					<image :src="'/static/index/home' + (pageCur=='1'?'_cur':'') + '.png'"></image>
				</view>
				<view :class="pageCur=='1'?'text-yellow':'text-gray'">首页</view>
			</view>
			<view class="action text-gray add-action">
				<button @click="requestTailwind" style="color: white; background-color: #F0AD4E;" class="cu-btn cuIcon-add bg-yellow add_bottom shadow"></button>
				发布
			</view>
			<view class="action" @click="navChange" data-cur='2'>
				<view class="cuIcon-cu-image">
					<image :src="'/static/index/my' + (pageCur=='2'?'_cur':'') + '.png'"></image>
				</view>
				<view :class="pageCur=='2'?'text-yellow':'text-gray'">我的</view>
			</view>
		</view>
	</view>
</template>
<script>
	import {
		host
	} from '../config/proxy.js'
	import {
		uniCard
	} from '@dcloudio/uni-ui/lib/uni-card/uni-card.vue'
	import {
		uniSearchBar
	} from '@dcloudio/uni-ui/lib/uni-search-bar/uni-search-bar.vue'
	import {
		uniNavBar
	} from '@dcloudio/uni-ui/lib/uni-nav-bar/uni-nav-bar.vue'
	import {
		uniIcons
	} from '@dcloudio/uni-ui/lib/uni-icons/uni-icons.vue'
	import {
		userCenter
	} from '../components/userCenter/userCenter.vue'
	export default {
		components: {
			uniCard,
			uniSearchBar,
			userCenter,
			uniNavBar,
			uniIcons
		},
		data() {
			return {
				pageCur: '1',
				tailwindRequestList: [],
				isLogin: false,
				clearShow: false,
				searchValue: '',
				border: false,
				statusBar: this.StatusBar,
				custom: this.CustomBar
			}
		},
		onLoad() {
			this.loadRequestList()
		},
		onPullDownRefresh() {
			// 下拉刷新
			this.tailwindRequestList = []
			this.loadRequestList()
			uni.stopPullDownRefresh()
		},
		methods: {
			navChange: function(e) {
				// 切换界面
				this.pageCur = e.currentTarget.dataset.cur
				if (this.pageCur == '1') {
					// 加载请求单信息
					this.loadRequestList()
				} else if (this.pageCur == '2') {
					// 加载个人信息

				}
			},
			requestTailwind: function() {
				uni.showToast({
					title: '你好啊'
				})
			},
			test: function(e) {
				console.log(e)
			},
			loadRequestList: function() {
				uni.request({
					url: host + 'tailwind/list/',
					method: 'GET',
					success: (res) => {
						if (res.statusCode == 200) {
							this.tailwindRequestList = []
							for (var i = 0; i < res.data.TailwindRequest.length; i++) {
								this.tailwindRequestList.push(res.data.TailwindRequest[i])
							}
						} else if (res.statusCode == 404) {
							uni.showToast({
								title: '服务器未连接',
								icon: 'none'
							})
						} else {
							uni.showToast({
								title: res.data.errMsg,
								icon: 'none'
							})
						}
					}
				})
			},
			// 搜索栏method
			search: function() {
				console.log(this.searchValue)
			},
			clickSearch: function() {
				this.clearShow = true
			},
			blurSearch: function() {
				this.clearShow = false
			},
			changeSearchValue: function(e) {
				if (this.clearShow == true) {
					this.searchValue = e.detail.value
				}
			},
			clearSeachValue: function() {
				this.searchValue = ''
				this.clearShow = false
			},
			clickScan: function() {
				uni.scanCode({
					scanType: 'qrCode',
					success(res) {
						console.log(res)
					}
				})
			}
		}
	}
</script>

<style>
	.nav-bar {
		width: 100%;
		height: 10vh;
		background: #F0AD4E;
		z-index: 1;
		position: fixed;
		top: 0;
	}

	.input-view {
		/* #ifndef APP-PLUS-NVUE */
		display: flex;
		/* #endif */
		flex-direction: row;
		/* width: 500rpx; */
		flex: 1;
		background-color: #f8f8f8;
		height: 30px;
		border-radius: 15px;
		padding: 0 10px;
		flex-wrap: nowrap;
		margin: 1rpx 0;
		line-height: 30px;
	}

	.nav-bar-input {
		height: 30px;
		line-height: 30px;
		/* #ifdef APP-PLUS-NVUE */
		width: 430rpx;
		/* #endif */
		padding: 0 5px;
		font-size: 28rpx;
		background-color: #f8f8f8;
	}
</style>
