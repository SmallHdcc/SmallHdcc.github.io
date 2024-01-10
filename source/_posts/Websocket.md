---
file-created: 2024 01 10
last-modified: 2024 01 10
---

### 为什么要用Websocket

我想在我的博客网站实现一个及时预警功能，在我的服务器失效之后能够通知到前端，起初我是通过轮询发送请求到服务器，看是否能响应成功。

后面我觉得这种方式实在是太呆了，而且还有很多问题，假如登录的用户都这样，服务器的压力就太大了，可能吧，我自己臆想的。

所以采用websocket的方式，它能够长时间保持会话，只需要在进入网页的时候开启一次就够了，之后采取发送心跳包的方式来检测。

### 出现的问题

线上的服务无法正确的与websocket建立连接，本地可以，本地也可以与远程的服务器连接，我使用在线测试工具也可以实现这个功能，但是就是我线上的Vite项目不可以。

将 ws://localhost:port/ 换成 wss://localhost:port 还是不行，我一度绝望，认为这个问题很奇怪，然而我到网上去搜索，发现还是有人有同样的问题。

### 最终解决

在vite.config.js中 假如 ws：false  表示 ws不走这个配置。 
就解决了，具体原因还亟待寻找。
```js
  server: {
    proxy: {
      "/api": {
        target: "http://localhost:8081",
        // target: "http://server_name:8081/",

        changeOrigin: true,
        rewrite: (path) => path.replace(/^\/api/, ""),
      },
      ws: false, 
    },
  },
```







