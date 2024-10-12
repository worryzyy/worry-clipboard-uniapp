<template>
	<view class="content" style="height: 100%" :scroll-y="true">
		<textarea class="inputArea mb-10" v-model="inputText" />
		<button class="mb-10" type="primary" @click="copyT">复制文本</button>
		<button class="mb-10" type="primary" @click="pasteT">粘贴文本</button>
		<textarea class="inputArea mb-10" v-model="copyText" />
		<view class="flex">
			<view class="w-50">
				<image style="width: 100%" src="/static/lixin.jpg" mode="aspectFit"></image>
				<button class="mb-10" type="primary" @click="copyLocalImage">复制本地图片</button>
			</view>
			<view class="w-50">
				<image style="width: 100%" src="https://env-00jxgxfkbq39.normal.cloudstatic.cn/share/lixin.jpg" mode="aspectFit"></image>
				<button class="mb-10" type="primary" @click="copyNetworkImage">复制网络图片</button>
			</view>
		</view>
		<button class="mb-10" type="primary" @click="pasteImg">粘贴图片</button>
		<view v-if="imgurl">
			<image :src="imgurl" mode="aspectFit"></image>
		</view>
		<button class="mb-10" type="warn" @click="clearCLIP">清除剪切板</button>
	</view>
</template>

<script setup>
import { ctrlC, ctrlV, ctrlCImg, ctrlVImg, clearCV } from '@/uni_modules/worry-clipboard';
import { ref } from 'vue';
const inputText = ref('加强李信');
const copyText = ref('');
const imgurl = ref('');

/*
 *复制文本
 */
const copyT = () => {
	ctrlC(inputText.value, (res) => {
		console.log(res);
		uni.showToast({
			icon: 'none',
			title: res.msg
		});
	});
};

/*
 *粘贴文本
 */
const pasteT = () => {
	ctrlV((res) => {
		console.log(res);
		if (res.data != '') {
			copyText.value = res.data;
		}
		uni.showToast({
			icon: 'none',
			title: res.msg
		});
	});
};

/*
 *  复制本地图片
 */
const copyLocalImage = () => {
	new Promise((resolve, reject) => {
		plus.io.resolveLocalFileSystemURL(
			'_www/static/lixin.jpg', // 这里本地图片地址需要加前缀 '_www',不然IOS端会提示 不允许读
			function (entry) {
				entry.file(
					function (file) {
						var reader = new plus.io.FileReader();
						reader.onloadend = function (e) {
							var base64 = e.target.result;
							resolve(base64.split(',')[1]);
						};
						reader.readAsDataURL(file);
					},
					function (e) {
						console.log('读写出现异常: ' + e.message);
						reject(e);
					}
				);
			},
			(err) => {
				console.log(err);
			}
		);
	}).then((base64str) => {
		console.log(base64str.indexOf('\n'));
		ctrlCImg(base64str, (res) => {
			console.log(res);
			uni.showToast({
				icon: 'none',
				title: res.msg
			});
		});
	});
};

/*
 *  复制网络图片
 */
const copyNetworkImage = () => {
	uni.downloadFile({
		url: 'https://env-00jxgxfkbq39.normal.cloudstatic.cn/share/lixin.jpg',
		success: (res) => {
			console.log(res);
			if (res.statusCode == 200) {
				const tempFilePath = res.tempFilePath;
				new Promise((resolve, reject) => {
					plus.io.resolveLocalFileSystemURL(tempFilePath, function (entry) {
						entry.file(
							function (file) {
								var reader = new plus.io.FileReader();
								reader.onloadend = function (e) {
									var base64 = e.target.result;
									resolve(base64.split(',')[1]);
								};
								reader.readAsDataURL(file);
							},
							function (e) {
								console.log('读写出现异常: ' + e.message);
								reject(e);
							}
						);
					});
				}).then((res) => {
					// console.log(res);
					ctrlCImg(res, (res) => {
						console.log(res);
						uni.showToast({
							icon: 'none',
							title: res.msg
						});
					});
				});
			}
		}
	});
};

/*
 *粘贴图片
 */
const pasteImg = () => {
	ctrlVImg((res) => {
		console.log(res);
		if (res.data != '') {
			imgurl.value = 'data:image/png;base64,' + res.data.replace(/\n/g, ''); // 返回的base64带 '\n'，删掉，也是找了好久才发现是这个问题
		}
		uni.showToast({
			icon: 'none',
			title: res.msg
		});
	});
};

/*
 *清楚剪切板
 */
const clearCLIP = () => {
	clearCV((res) => {
		console.log(res);
		uni.showToast({
			icon: 'none',
			title: res.msg
		});
	});
};
</script>

<style>
.content {
	margin: auto;
	padding: 10px;
}

.flex {
	display: flex;
	align-items: center;
	justify-content: space-between;
	flex-direction: row;
}

.w-50 {
	width: 48%;
}

.mb-10 {
	margin-bottom: 10px;
}

.content .inputArea {
	width: 100%;
	height: 80px;
	border: 1px solid #1890ff;
}
</style>
