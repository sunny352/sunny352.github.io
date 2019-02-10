---
layout: post
title:  "websocket 实例：egret 使用typescript"
date: 2019-02-10 20:10:27 +0800
tags: [websocket, egret, client, typescript]
---

<!-- # websocket 实例：egret 使用typescript -->

> 使用egret引擎开发websocket只需简单的添加几个事件响应即可，无需手动添加线程或循环<br>
> 以下示例基于egret5.2.13

```typescript
class WSNet {
    //单例，方便使用
    public static Inst(): WSNet {
        if (null == WSNet._inst) {
            WSNet._inst = new WSNet();
        }
        return WSNet._inst;
    }
    private static _inst: WSNet;

    //websocket连接对象
    private socket: egret.WebSocket;
    //构造函数
    constructor() {
        this.socket = new egret.WebSocket();
        //设置websocket运行模式为字符串模式，方便进行json序列化
        this.socket.type = egret.WebSocket.TYPE_STRING;
        //添加收到消息响应
        this.socket.addEventListener(egret.ProgressEvent.SOCKET_DATA, this.onReceive, this);
        //添加连接成功响应
        this.socket.addEventListener(egret.Event.CONNECT, this.onConnected, this);
        //添加断开连接响应
        this.socket.addEventListener(egret.Event.CLOSE, this.onDisconnected, this);
        //添加网络IO错误响应
        this.socket.addEventListener(egret.IOErrorEvent.IO_ERROR, this.onError, this);
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
        this.socket.writeUTF(JSON.stringify(obj))
    }

    //连接成功
    private onConnected(): void {
        egret.log("on connect");
    }
    //断开连接
    private onDisconnected(): void {
        egret.log("on disconnect")
    }
    //收到消息，这里会将收到的消息当作json文本并转换为对象提交给之前设置的收到消息的回调中去
    private onReceive(e: egret.Event): void {
        var jsonStr = this.socket.readUTF();
        if (null == this.receiveCallback) {
            return;
        }
        var jsonObj = JSON.parse(jsonStr);
        this.receiveCallback.call(this.receiveThisObject, jsonObj);
    }
    //错误处理
    private onError(): void {
        egret.log("on error")
    }
}
```