---
title: Websocket的使用
top: false
cover: false
toc: true
mathjax: true
comments: true
date: 2023-07-12 15:48:35
password:
summary:
tags:
- 前端
- Vue
categories:
- [前端, Vue ]

id: 1
---
Websocket在项目中的应用
<!--More-->

# 在Vue3 中使用 Websocket
## 在utils目录下新建 webSocket.js文件
```js codes
/**
 * 参数说明：
 *  webSocketURL：String    webSocket服务地址    eg: ws://127.0.0.1:8088/websocket (后端接口若为restful风格可以带参数)
 *  callback：为带一个参数的回调函数
 *  message：String 要传递的参数值（不是一个必要的参数）
 */
export default{
    // 初始化webSocket
    webSocketInit(webSocketURL){      // ws://127.0.0.1:8088/websocket
        this.webSocket = new WebSocket(webSocketURL);
        this.webSocket.onopen = this.onOpenwellback;
        this.webSocket.onmessage = this.onMessageCallback;
        this.webSocket.onerror = this.onErrorCallback;
        this.webSocket.onclose = this.onCloseCallback;
    },

    // 自定义回调函数
    setOpenCallback(callback){ //  与服务端连接打开回调函数
        this.webSocket.onopen = callback;
    },
    setMessageCallback(callback){   //  与服务端发送消息回调函数
        this.webSocket.onmessage = callback;
    },
    setErrorCallback(callback){ //  与服务端连接异常回调函数
        this.webSocket.onerror = callback;
    },
    setCloseCallback(callback){ //  与服务端连接关闭回调函数
        this.webSocket.onclose = callback;
    },

    close(){
        this.webSocket.close();
    },
    sendMessage(message){
        this.webSocket.send(message);
    },
}
```
## 在文件中引用
```js codes
  import webSocket from '@/utils/webSocket';
```
## 使用
```js codes
// 初始化websocket
function getWebsocket(id) {
  const webSocketUrl = import.meta.env.VITE_APP_SOCKET_API;
  webSocket.webSocketInit(webSocketUrl + `/websocket/platform${id}`); //初始化webSocket
  webSocket.setOpenCallback((res) => {
    console.log("连接建立成功",res);
  });
  webSocket.setMessageCallback((res) => {
    const resData = JSON.parse(res.data);
    console.log('接收到回信', resData);
    const { data, type } = resData;
    // 根据不同的type 去执行不同的操作 
    // 登录
    if (type == '3') {
      const { nickName, userDept, userName } = data;
      ···
      // 退出
    } else if (type == '4') {
      ···
    } else if (type == '5') {
      ···
    } else if (type == '1') {
      ...
    }
  });
  webSocket.setErrorCallback((res) => {
    console.log('连接异常', res);
  });
```
# uniapp（vue2）中使用websocket
```js codes
// 初始化时调用
  this.connectSocket()
  ...
//连接websocket
    connectSocket() {
      let that = this;
      console.log('调用连接websocket');
      this.socketTask = uni.connectSocket({
        url:
          globalConfig.webSocketUrl +
          `/websocket/uniapp${this.$store.state.userInfo.user.userId}`,
        success(res) {
          console.log('websocket连接成功');
        },
        fail(err) {
          console.log('报错', err);
        },
      });
      this.socketTask.onOpen(function (res) {
        console.log('WebSocket连接已打开！');
        that.heart()
      });
      this.socketTask.onMessage(function (res) {
        const resData = JSON.parse(res.data);
        console.log('接收到回信', resData);
        const { data, type } = resData;
        if (type == '6') {
        }
      });
      this.socketTask.onError(function (res) {
        console.log('WebSocket连接打开失败，请检查！');
        console.log(res);
        //进入重新连接
        that.reconnect();
      });
      // // 监听连接关闭 -
      this.socketTask.onClose((e) => {
        console.log('WebSocket连接关闭！');
        // clearInterval(that.timer)
        // that.timer = ''
        // if (!that.isClose) {
        // that.reconnect()
        // }
      });
      // console.log(this.socketTask)
    },
    //进入重新连接
    reconnect() {
      console.log('进入断线重连');
      // this.socketTask.close();
      this.socketTask = null;
      this.connectSocket();
    },
    //发送消息
    sendSocketMessage(msg) {
      console.log('发送信息');
      console.log(msg);
      return new Promise((reslove, reject) => {
        this.socketTask.send({
          data: msg,
          success(res) {
            console.log('发送成功');
            reslove(res);
          },
          fail(res) {
            console.log('发送失败');
            console.log(res);
            reject(res);
          },
        });
      });
    },

    //心跳
    heart() {
      let that = this;
      clearInterval(this.timer);
      this.timer = '';
      let msg = {
        type: 'heartbeat',
      };
      this.timer = setInterval(() => {
        that
          .sendSocketMessage(JSON.stringify(msg))
          .then((res) => {
            console.log('心跳成功');
          })
          .catch((res) => {
            console.log('发送失败');
            console.log(res);
          });
      }, 200000);
    },
```
[:heart:筱何仔]{.label .danger}