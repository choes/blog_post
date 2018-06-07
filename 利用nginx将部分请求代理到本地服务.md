---
title: 利用Nginx将部分请求代理到本地服务
date: 2018-06-05 18:25:07
tags: [Nginx,WebSocket,代理]
---
大多数服务的地址不会直接暴露出来，和app之间都会有个API网关。但开发时为了调试方便需要将app的部分请求导到本地服务来处理。

以前我是通过直接改dns配置，将API网关的域名的IP地址配成我本机的，但本地没有搭建所有的服务(比如运营系统)，某些请求收不到回复的时候app会转很久的圈圈。

后面尝试用Charles工具的"Map Remote"功能，但是它没能将WebSocket数据Map到我本机，试了搜索到的解决方法也没凑效。

最后一想Nginx不就是做这个代理功能的吗？果然用几行配置就搞定了！

##### 将WebSocket数据和HTTP请求代理到不同的服务
配置文件nginx.conf参考了"[配置Nginx反向代理WebSocket](https://www.hi-linux.com/posts/42176.html)"这篇文章。
```
    # WebSocket服务搭建在我本地的9090端口，Nginx服务也在我本机
    upstream websocket {
        server localhost:9090;
    }

    upstream api {
        server test.xxx.xxx.com:8080; # API网关地址
    }
	
    # 登录请求代理到我本地
    location /xxx/login {
        proxy_pass http://websocket;
        proxy_read_timeout 300s;
		
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;

        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection $connection_upgrade;
    }

    # 其它请求代理给API网关处理
    location /xxx/ {
        proxy_pass http://api;
    }
```

{% asset_img nginx_proxy.png access.log %}
101是WebSocket握手成功的HTTP响应码，200是API网关返回的HTTP响应码。

就是这么简单！
