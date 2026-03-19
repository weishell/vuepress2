---
title: 应用-视频相关
date: 2025/03/30
tags:
 - js
 - video
categories:
 - 应用
---


## 前端视频

H5支持VIDEO标签的使用，使得前端可以更好的去实现视频播放的功能，在此基础上，出现了很多的播放器。

### xgplayer

#### 弹幕功能
```js
	// 1. 弹幕插件不是很常用且代码量较大，所有并没有加入预定义preset中，需要单独引用
	// 2. 该插件中弹幕功能依赖 danmu.js 实现，需单独安装
	import 'xgplayer/dist/index.min.css'
	import Danmu from 'xgplayer/es/plugins/danmu'
	import 'xgplayer/es/plugins/danmu/index.css'
	import Player, {
		Events
	} from 'xgplayer'
	const playerInstance = new Player({
		id: 'xxxx',
		url: 'https://xxxx.xxx.com',
		danmu: {
			comments: [{
					duration: 1500,
					id: '2',
					start: 3000,
					txt: 'vue是世界上最好的前端框架',
					mode: 'top',
					color: true,
					style: {
						color: '#FF2211'
					}
				},
				{
					duration: 150000,
					id: '3',
					start: 4000,
					txt: '扯犊子呢，肯定是react啊',
					mode: 'bottom',
					style: {
						color: 'green',
						fontSize: '26px'
					}
				},
				{
					duration: 150000,
					id: '4',
					start: 5000,
					txt: '我是一条移动的弹幕',
					mode: 'scroll',
					color: true,
					style: {
						color: 'blue'
					}
				},
				{
					duration: 150000,
					id: '5',
					start: 8000,
					txt: '长弹幕长弹幕长弹幕',
					mode: 'scroll',
					style: {
						color: '#b2253c'
					}
				},
				{
					duration: 100000,
					id: '6',
					start: 2000,
					txt: '居中的弹幕',
					mode: 'scroll',
					style: {
						color: '#b2253c'
					}
				},
				{
					duration: 100000,
					id: '7',
					start: 3000,
					txt: 'something I will xxxxx xxxx yyyy',
					mode: 'scroll',
					style: {
						color: '#b2253c'
					}
				}
			],
			area: {
				start: 0,
				end: 1
			},
			closeDefaultBtn: false,
			defaultOff: false,
			panel: true
		},
		plugins: [Danmu], //
		height: 'xxx', 
		width: 'yyy',
	})
```



### plyr



### 视频的注意事项

> 视频如果默认实现分片操作，状态码为`206`，后端如果再包一层，可能会造成浏览器自身的分片失效，会**持续下载整个视频**。需要注意！

> 2.4MB transferred over network， resourcesize：10.5MB,当实现了206分片后，悬浮到network上可以看到resourcesize会持续变化，当拖动滚动条时，会有新的一次请求发生
