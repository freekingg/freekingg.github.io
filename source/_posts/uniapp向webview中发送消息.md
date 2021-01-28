---
title: uniapp向webview中发送消息
date: 2021-01-28 10:46:33
tags:
- uniapp
categories:
- 移动端
---

# uniapp向webview中发送消息

在业务开发中，使用webview嵌套了vue开发的h5页面，进行套壳打包，其一需求是，在h5中获取当前app的版本号，就涉及到了uniapp向webview传递消息的场景

## uniapp 中代码
核心为获取到webview窗口，然后通过evalJS执行h5中的方法
- setVersion方法为h5页面中定义的全局方法
```
<template>
	<view class="content">
		<!-- 开发 -->
		<web-view @message="onMessage" :webview-styles="webviewStyles" src="http://192.168.1.158:3000"></web-view>
		
	</view>
</template>
<script>
export default {
	data() {
		return {
			title: 'Hello',
			webviewStyles: {
				progress: {
					color: '#f0b90b'
				}
			},
			wv: null,
		};
	},
	onLoad() {
		const self = this;
		// #ifdef APP-PLUS
		let currentWebview = this.$scope.$getAppWebview();
		setTimeout(function() {
			let wv = currentWebview.children()[0];
			self.wv = wv;
			self.setVersion();
		}, 1000);
		// #endif
		
	},
	methods: {
		// 设置版本号
		setVersion() {
			const self = this;
			plus.runtime.getProperty(plus.runtime.appid, function(widgetInfo) {
				self.wv.evalJS("setVersion('" + widgetInfo.version + "')");
			});
		},
		// 接收消息
		onMessage(e) {
			const self = this;
			console.log('我收到了网页发来的消息', e.detail.data);
			let data = e.detail.data;
		},
	}
};
</script>

<style>
.content {
	background-color: red;
}
</style>

```
## h5 中代码
App.vue
```
// 设置版本号
    window.setVersion = (id) => {
      localStorage.setItem("version", version);
    };
```

然后就可以在h5中获取使用了

