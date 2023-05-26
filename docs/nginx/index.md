## nginx 
### 基础常识
```
一 基本介绍
1 main配置（全局配置）在其它地方都可见，影响其它所有的配置
2 events（工作进程相关配置）
3 http（可以包含多个server）
4 upstream(上游配置，负载均衡相关(后端接口有多个ip地址可以进行访问),配置负载均衡的策略)
5 server (虚拟主机配置,包含多个location,用于请求处理)
6 location (url匹配后特定的配置)

main>http> upstream | server >location （server继承main,location继承server）

二 nginx是轻量级(占用内存少,体积小)，高性能（高并发连接数,异步非阻塞事件处理机制，epoll模型,提供队列）的 web服务器
（能够处理http请求并返回响应[apache tomcat iis nginx 都是web服务器]） 和 反向代理服务器。

三 nginx的用途
  #正向代理和反向代理。
  3.1 正向代理的服务对象是客户端，也叫“客户端代理”.它会对真实的服务端隐藏真实的客户端。可以保护客户端隐私，
  帮助客户端在访问目标服务器时绕过一些防火墙限制和安全规则。

  正向代理服务器:<它代表客户端(发送请求)到目标服务器>
  [b->ng(proxy_pass )->s->ng->b] 
  客户端知道目标服务器是什么，但它不能直接进行访问。正向代理可以使得客户端能够间接访问服务端,同时
  服务端只知道是代理服务器访问了它，不知道源头的客户端是什么。源客户端也不知道目标服务器背后的工作机制。

  3.2 反向代理的服务对象是服务端，也叫“服务端代理”.它会对真实的客户端隐藏真实的服务端。它的目的是
  隐藏目标服务器的真实IP地址和提供一些额外的功能，例如负载均衡和缓存等。proxy_pass + upstream
  反向代理服务器：<它代表目标服务器向(发送响应)到客户端>
  [b->ng(proxy_pass + upstream)->s->ng->b]。
  客户端可以直接访问目标服务器,目标服务服务器知道源客户端。

  3.3 小结 :服务端是否能够感知到源客户端，服务端是否被源服务端直接访问，这是正向代理和反向代理的区分标准。
  
  #动["正向和反向代理"]静["静态资源文件读取"]结合.
  
  3.4 可以配置特定的静态资源文件从nginx所在服务器指定的服务器本地目录下读取。
  server{
    listen 80;
    location ~ ^.*\.(png|jpg|gif)$ {
    gzip on;
    root /soft/code/images;
    }
  }  
```
#### main配置
```
主要配置工作进程和连接数相关的内容。
以下是全局块的主要配置
1 指定可以运行nginx服务器的用户组指令。只能在全局块进行配置 user [user] [group] 如：
user nobody nobody,意义是不进行限制。
2 指定工作进程数。通常是cpu核数的2n的倍数。worker_processes 2;
3 指定nginx唯一标识符, 进程id配置,pid 配置。只能在全局块中进行配置。 pid logs/nginx.pid。在Nginx中，PID (Process ID) 
是指运行Nginx进程的唯一标识符，它可以用来管理和监控Nginx进程,设置PID的用意是为了方便系统管理者.
对Nginx进程的管理和监控，以及操作系统对Nginx的进程进行识别和处理。
4 指定nginx错误日志存放位置.可以在全局块，http块,server块，location块中进行配置。error_log/access.log
```
#### events块 配置
```
主要是工作进程的关联配置。
1 worker_connections 1024 //设置每个工作进程的最大连接数

events {
  worker_connections 1024;
}

```
#### http块配置
```
请求处理过程中的核心配置。包含upstream,server模块的配置。
include指令可以出现在http块中，也可以出现在server块中，也可以出现在locaiton中。
```
#### upstream块配置
```
配置负载的策略和服务器
upstream {
  #least_conn
  #ip_hash
  #..
}
```

#### server块虚拟主机配置
```
server {
  listne 80;
  server_name www.test.com; 
}

```
#### location块规则设置
```
对服务器的请求进行匹配，如果都没有命中会进入默认的处理模块
location / {//没命中location的其它匹配块就进这里处理。

}
```

