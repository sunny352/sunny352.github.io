---
layout: post
title:  "websocket 实例：go client"
date: 2019-02-04 20:22:35 +0800
tags: [websocket, go, client]
---

<!-- # websocket 实例：go client -->

> go 语言中 websocket 的客户端示例。<br>
> 具体的示例代码在👉[这里](https://github.com/sunny352/Example_Websocket)👈

```go
package main

import (
	"fmt"
	"github.com/gorilla/websocket"//这里使用的是 gorilla 的 websocket 库
	"log"
	"time"
)

func main() {
	//创建一个拨号器，也可以用默认的 websocket.DefaultDialer
	dialer := websocket.Dialer{}
	//向服务器发送连接请求，websocket 统一使用 ws://，默认端口和http一样都是80
	connect, _, err := dialer.Dial("ws://127.0.0.1/", nil)
	if nil != err {
		log.Println(err)
		return
	}
	//离开作用域关闭连接，go 的常规操作
	defer connect.Close()

	//定时向客户端发送数据
	go tickWriter(connect)

	//启动数据读取循环，读取客户端发送来的数据
	for {
		//从 websocket 中读取数据
		//messageType 消息类型，websocket 标准
		//messageData 消息数据
		messageType, messageData, err := connect.ReadMessage()
		if nil != err {
			log.Println(err)
			break
		}
		switch messageType {
		case websocket.TextMessage://文本数据
			fmt.Println(string(messageData))
		case websocket.BinaryMessage://二进制数据
			fmt.Println(messageData)
		case websocket.CloseMessage://关闭
		case websocket.PingMessage://Ping
		case websocket.PongMessage://Pong
		default:

		}
	}
}

func tickWriter(connect *websocket.Conn) {
	for {
		//向客户端发送类型为文本的数据
		err := connect.WriteMessage(websocket.TextMessage, []byte("from client to server"))
		if nil != err {
			log.Println(err)
			break
		}
		//休息一秒
		time.Sleep(time.Second)
	}
}
```