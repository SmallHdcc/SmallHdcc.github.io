---
title: Websocket
date: 2024-01-10 12:09:22
file-created: 2024 01 10
last-modified: 2024 01 18
tags:
  - 知识
categories:
  - 技术
---

### 最终的结果是放弃使用websocket

我要做的是检查服务器的通信状态，使用websocket实在是大材小用，主要是我也没用好

websocket 是先发送http请求到达服务器，然后再转化为ws的，所以需要再jwt那里放行，这才是我未登录情况下一直连接失败的原因。

这个功能我是使用响应拦截器来实现的，返回不正常的响应就提示用户吗，用户只看文章的话，服务器失效，是不会受影响的。

如果需要有bug需要维护，在nginx那里也可以配置，当服务器需要完善的时候打开注释并且重启nginx就好了。
```nginx
# rewrite ^(.*)$ /error.html break;
```



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


### 在我们的springboot项目中

首先导入 
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-websocket</artifactId>
    <version>3.1.2</version>
    <scope>compile</scope>
</dependency>
```

其次创建 `WebSocketConfig` 文件，记得实现 `WebSocketConfigurer`
```java
@Configuration
@EnableWebSocket
@Slf4j
public class WebSocketConfig implements WebSocketConfigurer {

    @Override
    public void registerWebSocketHandlers(WebSocketHandlerRegistry registry) {
        log.info("websocket config start...");
        //添加 websocket处理器 并设置跨域 
        registry.addHandler(new MyWebSocketHandler(), "/websocket").setAllowedOrigins("*");
    }
}
```
`log.info`后面的是日志信息
如果配置是上面那样的，那么你的websocket连接的路径应该是
ws://localhost:port/websocket 


### 在前端项目中（Vite）

在`App.vue`文件中


这个是服务失效之后的提示函数
```js
const notificatServerState = () => {
  ElNotification({
    title: '提示',
    message: '服务器失效',
    type: 'error',
  })
}
```

创建websocket连接
```js
const socket = new WebSocket('ws://localhost:8081/websocket')

onMounted(() => {
  socket.onopen = () => {
    console.log('Connected to server');
  }

  //发送心跳包
  setInterval(() => {
    if (socket.readyState == WebSocket.OPEN)
      socket.send('heartbeat');
  }, 10000);
  //接收消息
  socket.onmessage = (message) => {
    if (message.data != "heartbeat") {
      notificatServerState()
    }
  }

  socket.onerror = (error) => {
    notificatServerState();
  }
  socket.onclose = () => {
    console.log('Disconnected from server');
    notificatServerState();
  }
})

onUnmounted(() => {
  socket.close();
})
```





