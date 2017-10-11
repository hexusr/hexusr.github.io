---
layout: post
title:  "SpringBoot-WebSocket练习"
date:   2017-07-30 22:33:31 +0800
categories: SpringBoot
---
### 1. 准备：
 IDE:Intelijj IDEA
 所需js:sockjs.min.js(SockJS的客户端脚本）+stomp.min.js（STOMP协议的客户端脚本）
 stomp.min.js:https://github.com/plusjade/jekyll-bootstrap.git
 sockjs.min.js:https://github.com/sockjs/sockjs-client.git
 
### 2. 开始：
 idea下新建 Spring Initializr项目，勾选Web+Thymlead+Websocket依赖。
 项目结构如下图所示：
 ![这里写图片描述](http://img.blog.csdn.net/20170730221336290?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcjhsOHE4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
 
#### （1）新建WebSocket的配置类-WebSocketConfig，需要在配置类上使用@Configuration
@EnableWebSocketMessageBroker
注解，其中@EnableWebSocketMessageBroker用来开启WebSocket支持。代码如下：

```
package com.example.demo.config;

import org.springframework.context.annotation.Configuration;
import org.springframework.messaging.simp.config.MessageBrokerRegistry;
import org.springframework.web.socket.config.annotation.AbstractWebSocketMessageBrokerConfigurer;
import org.springframework.web.socket.config.annotation.EnableWebSocketMessageBroker;
import org.springframework.web.socket.config.annotation.StompEndpointRegistry;

@Configuration
@EnableWebSocketMessageBroker
public class WebSocketConfig extends AbstractWebSocketMessageBrokerConfigurer {
    @Override
    //注册STOMP协议的节点,并映射到指定的url
    public void registerStompEndpoints(StompEndpointRegistry stompEndpointRegistry) {
        //注册一个STOMP的endpoint，并指定使用SockJS协议
        stompEndpointRegistry.addEndpoint("/endpoint").withSockJS();
    }

    @Override
    public void configureMessageBroker(MessageBrokerRegistry registry) {
        //配置消息代理（Message Broker)
//        super.configureMessageBroker(registry);
        registry.enableSimpleBroker("/topic");
    }
}
```
#### （2）新建消息类-Message，接受来自浏览器的消息，代码如下：

```
package com.example.demo.bean;
//用作接受浏览器消息的参数，用次类接收来自浏览器的消息
public class Message {
    private String name;
    public String getName() {
        return name;
    }
}
```
#### （3)SocketResponse

```
package com.example.demo.bean;

public class SocketResponse {
    private String responseMessage;
    public SocketResponse(String responseMessage){
        this.responseMessage=responseMessage;
    }

    public String getResponseMessage() {
        return responseMessage;
    }
}

```
#### （4）ScoketController

```
package com.example.demo.controller;

import com.example.demo.bean.SocketResponse;
import com.example.demo.bean.Message;
import org.springframework.messaging.handler.annotation.MessageMapping;
import org.springframework.messaging.handler.annotation.SendTo;
import org.springframework.stereotype.Controller;

@Controller
public class SocketController {
    @MessageMapping("/welcome")
    @SendTo("/topic/getResponse")
    public SocketResponse say(Message msg) throws Exception{
        Thread.sleep(3000);
        return  new SocketResponse("Welcome,"+msg.getName()+"!");
    }
}

```
#### （5）ws.html

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8"/>
    <title>Title</title>
</head>
<body onload="disconnect()">
<noscript><h2 style="color:#ff0000">貌似你的浏览器不支持websocket</h2> </noscript>
<div>
    <div>
        <button id="connect" onclick="connect();">连接</button>
        <button id="disconnect" disabled="disabled" onclick="disconnect();">断开连接</button>
    </div>
    <div id="conversationDiv">
        <label>输入你的名字</label><input type="text" id="name"/>
        <button id="sendName" onclick="sendName();">发送</button>
    <p id="response"></p>
    </div>
</div>
<script th:src="@{sockjs.min.js}"></script>
<script th:src="@{stomp.min.js}"></script>
<script th:src="@{jquery-3.2.1.min.js}"></script>
<script type="text/javascript">
    var stompClient=null;
    function setConnected(connected) {
        document.getElementById('connect').disabled=connected;
        document.getElementById('disconnect').disabled=!connected;
        document.getElementById('conversationDiv').style.visibility=connected?'visible':'hidden'
        $('response').html();
    }
    function connect() {
        var socket=new SockJS('/endpoint');//连接SockJS的endpoint名称为”/endpoint"
        stompClient=Stomp.over(socket);//使用STOMP子协议的WebSocket客户端
        stompClient.connect({},function (frame) {//连接WebSocket服务端
            setConnected(true);
            console.log('Connected:'+frame);
            stompClient.subscribe('/topic/getResponse',function (response) {
                //通过stompClient.subscribe订阅/topic/getResponse目标发送的消息，这个是在控制器的@SendTO中定义的
                showResponse(JSON.parse(response.body).responseMessage);
            })
        })
    }
    function disconnect() {
        if(stompClient!=null){
            stompClient.disconnect();
        }
        setConnected(false);
        console.log('Disconnected');
    }
    function sendName() {
        var name=$("#name").val();
       // alert(name);
        //通过stompClient.send向/welcome目标发送消息，这个实在控制器@MessageMapping中定义的
        stompClient.send("/welcome",{},JSON.stringify({'name':name}));
    }
    function showResponse(message) {
        var response=$("#response");
        response.html(message);
    }
</script>
</body>
</html>
```
#### （6）WebMvcConfig

```
package com.example.demo.config;

import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.ViewControllerRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurerAdapter;
@Configuration
public class WebMvcConfig extends WebMvcConfigurerAdapter{
    @Override
    public void addViewControllers(ViewControllerRegistry registry) {
//        super.addViewControllers(registry);
        registry.addViewController("/ws").setViewName("/ws");
    }
}

```
#### （7）运行结果
![这里写图片描述](http://img.blog.csdn.net/20170730223321122?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcjhsOHE4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)