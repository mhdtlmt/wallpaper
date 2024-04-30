<template>
	<view class="classItem">
		<view class="loadingLayout" v-if="!classList.length && !noData">
			<uni-load-more status="loading"></uni-load-more>
		</view>

		<view class="content">
			<navigator :url="'/pages/preview/preview?id='+item._id" class="item" v-for="item in classList"
				:key="item._id">
				<image :src="item.smallPicurl" mode="aspectFill"></image>
			</navigator>
		</view>

		<view class="loadingLayout" v-if="classList.length || noData">
			<uni-load-more :status="noData?'noMore':'loading'"></uni-load-more>
		</view>
	</view>
</template>

<script setup>
	import {
		ref
	} from "vue";
	import {
		apiGetClassList
	} from "@/api/apis.js"
	import {
		onLoad,
		onReachBottom,
		onShareAppMessage,
		onShareTimeline
	} from "@dcloudio/uni-app"
	const queryParams = {
		pageNum: 1,
		pageSize: 12
	}
	let pageName;
	const classList = ref([]);
	const noData = ref(false);


	onLoad((e) => {
		let {
			id = null, name = null
		} = e;
		queryParams.classid = id;
		pageName = name
		uni.setNavigationBarTitle({
			title: name
		})

		getClassList()
	})

	onReachBottom(() => {
		if (noData.value) return
		console.log("到底了。。。")
		queryParams.pageNum++;
		getClassList();
	})

	const getClassList = async () => {
		let res = await apiGetClassList(queryParams);
		classList.value = [...classList.value, ...res.data];
		if (queryParams.pageSize > res.data.length) {
			noData.value = true
		}
		uni.setStorageSync("storgClassList", classList.value)
		console.log(classList.value)
	}


	//分享给好友
	onShareAppMessage((e) => {
		return {
			title: "咸虾米壁纸-" + pageName,
			path: "/pages/classlist/classlist?id=" + queryParams.classid + "&name=" + pageName
		}
	})


	//分享朋友圈
	onShareTimeline(() => {
		return {
			title: "咸虾米壁纸-" + pageName,
			query: "id=" + queryParams.classid + "&name=" + pageName
		}
	})
</script>

<style lang="scss" scoped>
	.classItem {
		.content {
			display: grid;
			grid-template-columns: repeat(3, 1fr);
			gap: 5rpx;
			padding: 5rpx;

			.item {
				height: 440rpx;

				image {
					width: 100%;
					height: 100%;
					display: block;
				}
			}

		}
	}
</style>