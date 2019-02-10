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

```typescript
export default class WSNet {
    //单例，方便使用
    public static Inst(): WSNet {
        if (null == WSNet._inst) {
            WSNet._inst = new WSNet();
        }
        return WSNet._inst;
    }
    private static _inst: WSNet;

    //websocket连接对象
    private socket: Laya.Socket;
    //构造函数
    constructor() {
        this.socket = new Laya.Socket();
        //添加收到消息响应
        this.socket.on(Laya.Event.MESSAGE, this, this.onReceive);
        //添加连接成功响应
        this.socket.on(Laya.Event.OPEN, this, this.onConnected);
        //添加断开连接响应
        this.socket.on(Laya.Event.CLOSE, this, this.onDisconnected);
        //添加网络IO错误响应
        this.socket.on(Laya.Event.ERROR, this, this.onError);
    }

    //收到消息的回调
    private receiveCallback: Function;
    //收到消息的回调对象
    private receiveThisObject: any;
    //设置收到消息的回调
    public setReceiveCallback(callback: Function, thisObject: any): void {
        this.receiveCallback = callback;
        this.receiveThisObject = thisObject;
    }
    //连接到服务器，这里使用url方式进行连接，即标准的websocket连接方式
    public connect(url: string): void {
        this.socket.connectByUrl(url);
    }
    //断开连接
    public disconnect(): void {
        this.socket.close();
    }
    //发送消息到服务器，这里会序列化为json
    public send(obj: any): void {
        this.socket.send(JSON.stringify(obj))
    }

    //连接成功
    private onConnected(): void {
        console.log("on connect");
    }
    //断开连接
    private onDisconnected(): void {
        console.log("on disconnect")
    }
    //收到消息，这里会将收到的消息当作json文本并转换为对象提交给之前设置的收到消息的回调中去
    private onReceive(message:any): void {
        var jsonStr = message
        if (null == this.receiveCallback) {
            return;
        }
        var jsonObj = JSON.parse(jsonStr);
        this.receiveCallback.call(this.receiveThisObject, jsonObj);
    }
    //错误处理
    private onError(): void {
        console.log("on error")
    }
}
```