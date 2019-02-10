---
layout: post
title:  "websocket 实例：LayaAir 使用typescript"
date: 2019-02-10 21:17:21 +0800
tags: [websocket, LayaAir, client, typescript]
---

<!-- # websocket 实例：LayaAir 使用typescript -->

使用LayaAir引擎开发websocket的流程和Egret类似，细节稍有不同。<br>
LayaAir封装的websocket可发送的数据类型只有两种：string和ArrayBuffer，分别对应文本传输方式和二进制传输方式，所以无需手动设置消息发送方式<br>
> 以下示例基于LayaAir2.0.0

<script src="https://gist.github.com/sunny352/8aa5569fb12a784c8666577716f56d94.js"></script>