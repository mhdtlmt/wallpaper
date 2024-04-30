<template>
	<view class="homeLayout pageBg">
		<custom-nav-bar title="推荐"></custom-nav-bar>
		<view class="banner">
			<swiper indicator-dots indicator-color="rgba(255,255,255,0.5)" indicator-active-color="#FFF" autoplay
				circular>
				<swiper-item v-for="item in bannerList" :key="item._id">
					<image :src="item.picurl" mode="aspectFill"></image>
				</swiper-item>
			</swiper>
		</view>
		<view class="notice">
			<view class="left">
				<uni-icons type="sound-filled" size="20"></uni-icons>
				<text class="text">公告</text>
			</view>
			<view class="center">
				<swiper vertical autoplay interval="1500" duration="300" circular>
					<swiper-item v-for="item in noticeList" :key="item._id">
						<navigator url="/pages/notice/detail">
							{{item.title}}
						</navigator>
					</swiper-item>
				</swiper>
			</view>
			<view class="right">
				<uni-icons type="right" size="16" color="#333"></uni-icons>
			</view>
		</view>
		<view class="select">
			<common-title>
				<template #name>
					每日推荐
				</template>
				<template #custom>
					<view class="date">
						<uni-icons type="calendar" size="18"></uni-icons>
						<view class="text">
							<uni-dateformat :date="Date.now()" format="dd日"></uni-dateformat>
						</view>
					</view>
				</template>
			</common-title>
			<view class="content">
				<scroll-view scroll-x>
					<view class="box" v-for="item in randomList" :key="item._id" @click="goPreview(item._id)">
						<image :src="item.smallPicurl" mode="aspectFill"></image>
					</view>
				</scroll-view>
			</view>
		</view>
		<view class="theme">
			<common-title>
				<template #name>
					专题精选
				</template>
				<template #custom>
					<navigator url="" class="more">More+</navigator>
				</template>
			</common-title>

			<view class="content">
				<theme-item :item="item" v-for="item in classifyList" :key="item._id"></theme-item>
				<theme-item :isMore="true"></theme-item>
			</view>
		</view>
	</view>
</template>

<script setup>
	import {
		ref
	} from 'vue';
	import {
		apiGetBanner,
		apiGetDayRandom,
		apiGetNotice,
		apiGetClassify
	} from "@/api/apis.js";
	import {
		onShareAppMessage,
		onShareTimeline
	} from "@dcloudio/uni-app"

	const bannerList = ref([]);
	const randomList = ref([]);
	const noticeList = ref([]);
	const classifyList = ref([]);

	const goPreview = (id) => {
		uni.setStorageSync("storgClassList", randomList.value);
		uni.navigateTo({
			url: "/pages/preview/preview?id=" + id
		})
	}

	const getBanner = async () => {
		let res = await apiGetBanner();
		bannerList.value = res.data;
	}

	const getNotice = async () => {
		let res = await apiGetNotice({
			select: true
		})
		noticeList.value = res.data
	}

	const getDayRandom = async () => {
		let res = await apiGetDayRandom();
		randomList.value = res.data
	}

	const getClassify = async () => {
		let res = await apiGetClassify({
			select: true
		});
		classifyList.value = res.data
	}

	//分享给好友
	onShareAppMessage((e) => {
		return {
			title: "咸虾米壁纸，好看的手机壁纸",
			path: "/pages/classify/classify"
		}
	})

	//分享朋友圈
	onShareTimeline(() => {
		return {
			title: "咸虾米壁纸，好看的手机壁纸"
		}
	})


	getBanner();
	getDayRandom();
	getNotice();
	getClassify();
</script>

<style lang="scss" scoped>
	.homeLayout {
		.banner {
			swiper {
				width: 750rpx;
				height: 340rpx;
				padding: 30rpx;

				swiper-item image {
					width: 100%;
					height: 100%;
					border-radius: 10rpx;
				}
			}

			swiper {
				width: 750rpx;
				height: 340rpx;
				padding: 30rpx;

				swiper-item image {
					width: 100%;
					height: 100%;
					border-radius: 10rpx;
				}
			}
		}

		.notice {
			width: 690rpx;
			height: 80rpx;
			background: #ccc;
			margin: 0 auto;
			line-height: 80rpx;
			border-radius: 40rpx;
			display: flex;

			.left {
				width: 140rpx;
				display: flex;
				align-items: center;
				justify-content: center;

				:deep() {
					.uni-icons {
						color: $brand-theme-color !important;
					}
				}

				.text {
					color: $brand-theme-color;
					font-size: 28rpx;
					font-weight: 600;
				}
			}

			.center {
				flex: 1;

				swiper {
					height: 100%;

					&-item {
						navigator {
							text-overflow: ellipsis;
							white-space: nowrap;
							overflow: hidden;
							height: 100%;
							font-size: 30rpx;
							color: #666;
						}
					}
				}
			}

			.right {
				width: 70rpx;
				display: flex;
				align-items: center;
				justify-content: center;
			}
		}

		.select {
			padding-top: 50rpx;

			.date {
				display: flex;
				align-items: center;
				justify-content: center;

				:deep() {
					.uni-icons {
						color: $brand-theme-color !important;
					}
				}

				.text {
					color: $brand-theme-color;
					margin-left: 5rpx;
				}
			}

			.content {
				width: 720rpx;
				margin-left: 30rpx;
				margin-top: 30rpx;
				white-space: nowrap;

				.box {
					width: 200rpx;
					height: 430rpx;
					display: inline-block;
					margin-right: 15rpx;

					image {
						width: 100%;
						height: 100%;
						border-radius: 10rpx;
					}
				}

				.box:last-child {
					margin-right: 30rpx;
				}
			}
		}
	}

	.theme {
		padding: 50rpx 0;

		.more {
			font-size: 30rpx;
			color: #888;
		}

		.content {
			margin-top: 30rpx;
			padding: 0 30rpx;
			display: grid;
			gap: 15rpx;
			grid-template-columns: repeat(3, 1fr);
		}

	}
</style>