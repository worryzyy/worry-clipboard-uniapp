# worry-clipboard

### 简介

**使用UTS开发的剪切板功能，用来复制粘贴文字、图片，支持从相册复制图片粘贴到应用，比如复制的图片可以在微信粘贴**

**暂未对齐 `uni.setClipboardData` `uni.getClipboardData	` 系统API，不喜勿喷**


### 使用方法

**导入插件后，在页面使用以下方式导入，或者导入示例项目，示例项目为Uniapp-x项目，Uniapp的示例项目在文档最后面 [传送门](#uniapp-demo)**

`import { ctrlC, ctrlV, ctrlCImg, ctrlVImg, clearCV  } from '@/uni_modules/worry-clipboard'`

### API

`ctrlC(text,callback)`：复制文字
`ctrlV(callback)`：粘贴文字
`ctrlCImg(base64Text,callback)`：复制图片
`ctrlVImg(callback)`：粘贴图片
`clearCV(callback)`：清除剪切板

#### 参数

text | base64Text {String} 

| 参数		| 类型		|  描述									|
|:----		|:----		|:----									|
| text		|  String	|  调用`ctrlC`时传入需要复制的文字		|
| base64Text|  String	|  调用`ctrlCImg`时传入图片的Base64编码	|


callback {function ({ msg, data })} 回调方法

| 参数	| 类型		|  描述										|
|:----	|:----		|:----										|
| msg	|  String	|  API调用结果描述							|
| data	|  String	|  粘贴文字返回空字符串，粘贴图片返回Base64编码	|

### 注意！！

如果需要使用复制粘贴图片的功能，请一定打自定义基座运行，如果是uniapp的项目，不要使用测试证书打自定义基座

#### Android自定义基座

找到`worry-clipboard/utssdk`目录下面的`AndroidManifest.xml`文件
![](https://mp-bc8d1f0a-3356-4a4e-8592-f73a3371baa2.cdn.bspapp.com/uts-clipboard/963e52129edc6e3ac51af680bcace00.png)
这三个地方需要换成你自己的包名，其中`android:authorities="io.dcloud.uniappx.provider"`节点的格式是`你的包名`+`.provider`

#### IOS自定义基座

Xcode需要15.2以上的版本才能调试自定义基座，如果是低版本的Xcode，修改了IOS端的插件代码就会报错
（我的Mac系统版本太低了，用不了15以上的Xcode，每次修改IOS端的代码都需要重新打基座🙉）

### 示例

```javascript
/* 
 *复制文本
 */
const copyT = () => {
	ctrlC(inputText.value, (res) => {
		console.log(res)
		uni.showToast({
			icon: "none",
			title: res.msg
		})
	})
}

/* 
 *粘贴文本
 */
const pasteT = () => {
	ctrlV(res => {
		console.log(res)
		if (res.data != '') {
			copyText.value = res.data
		}
		uni.showToast({
			icon: "none",
			title: res.msg
		})
	})
}

/*
*  复制本地图片
*/
const copyLocalImage = () => {
	const FileManage = uni.getFileSystemManager();
	FileManage.readFile({
		encoding: "base64",
		filePath: '/static/logo.png',
		success: (res) => {
			// console.log(res.data);
			ctrlCImg(res.data, (res) => {
				console.log(res);
				uni.showToast({
					icon: "none",
					title: res.msg
				})
			})
		}
	} as ReadFileOptions)

}

/* 
 *  复制网络图片
 */
const copyNetworkImage = () => {
	uni.downloadFile({
		url: 'https://xxx.jpg', // 线上图片
		success: (res) => {
			if (res.statusCode == 200) {
				const FileManage = uni.getFileSystemManager();
				const tempFilePath = res.tempFilePath;
				FileManage.readFile({
					encoding: "base64",
					filePath: tempFilePath,
					success: (res) => {
						// console.log(res.data);
						ctrlCImg(res.data, (res) => {
							// console.log(res);
							uni.showToast({
								icon: "none",
								title: res.msg
							})
							// 网络图片复制完删除掉
							FileManage.unlink({
								filePath: tempFilePath,
								success: (res : FileManagerSuccessResult) => {
									console.log('删除 ', res);
								}
							} as UnLinkOptions)
						})
					}
				} as ReadFileOptions)
			}
		}
	})
}

/* 
 *粘贴图片
 */
const pasteImg = () => {
	ctrlVImg(res => {
		if (res.data != '') {
			imgurl.value = 'data:image/png;base64,' + res.data
		}
		uni.showToast({
			icon: "none",
			title: res.msg
		})
	})
}


/* 
 *清除剪切板
 */
const clearCLIP = () => {
	clearCV(res => {
		console.log(res)
		uni.showToast({
			icon: "none",
			title: res.msg
		})
	})
}

```

### Tips

复制图片`ctrlCImg(data,callback)`传入的Base64编码与粘贴图片`ctrlVImg(callback)`返回的Base64编码均不带`data:image/png;base64,`

### Uniapp-demo
[项目下载(vue3)](https://mp-bc8d1f0a-3356-4a4e-8592-f73a3371baa2.cdn.bspapp.com/uts-clipboard/worry-clipboard-uniapp.rar)

### 赞赏

如果觉得插件对你有帮助 buy me a coffee，感谢各位大佬

<div><img src="https://mp-bc8d1f0a-3356-4a4e-8592-f73a3371baa2.cdn.bspapp.com/native-dialog/Appreciate.jpg" width="400"/></div>

### 完结撒花 ✿✿ヽ(°▽°)ノ✿

