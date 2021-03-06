### Web缓存架构

web开发，怎么都离不开缓存的问题。  

举个例子：一般玩的单机游戏，往往都容量都非常大（几十G，乃至上百G），轻轻松松都超过了电脑内存（8G,16G），所以很明显的，在游戏的运行过程中，不可能整个游戏都放入内存中。如果都放在硬盘，或者移动硬盘里面，每次都要去读取，读取速度慢怎么办呢。该怎么解决问题？

这里就要说一下缓存局部原理了，可以分为时间和空间两个维度。

- 时间维度：曾经或者现在使用的数据，往往在之后会再次被使用
- 空间维度：被使用数据的关联数据，往往会被使用

局部性原理是在内存，高速缓存部分，提出来用于解决问题的

**缓存是一个到处都存在的用空间换时间的例子。通过使用多余的空间，我们能够获取更快的速度。**



#### 分类

- 1.浏览器缓存

  - Cookie
  - LocalStorage
  - SessionStorage
  - 基于浏览器的强缓存和缓存协商（last-modified,expries,cache-control,Etag）

- 2.CDN缓存

  - CDN边缘节点缓存

- 3.代理服务器缓存

  - Nginx缓存模块

- 4.数据库缓存

  - Mysql

- 5.外部缓存

  - Redis


#### 浏览器缓存。


一.cookie

>由于HTTP协议是无状态的，而服务器端的业务必须是要有状态的。Cookie诞生的最初目的是为了存储web中的状态信息，以方便服务器端使用。比如判断用户是否是第一次访问网站。目前最新的规范是RFC 6265，它是一个由浏览器服务器共同协作实现的规范。

特点：

　1.域的限制

　　2.时间限制

　　3.空间限制，cookie只能存储4kb

　　4.数量限制，每个域下最多不能超过50个cookie

　　5.存储数据类型限制，cookie只能存储字符串
　　
 
注意：cookie是不安全的，不能存一些私密数据。另外cookie是可以通过http请求的方式在http请求头被带到后端服务器的。

二.LocalStorage && SessionStorage


|  特性   | LocalStorage  | SessionStorage  | 
|  ----  | ----  | ----  | 
| 生命周期  | 除非被主动清楚，不然会一直存在浏览器 | 窗口会话期间
| 存放数据大小  | 5M 或者更大（不同浏览器可能不同 |5M 或者更大（不同浏览器可能不同）

>不同浏览器无法共享localStorage或sessionStorage中的信息。相同浏览器的不同页面间可以共享相同的 localStorage（页面属于相同域名和端口），但是不同页面或标签页间无法共享sessionStorage的信息。这里需要注意的是，页面及标 签页仅指顶级窗口，如果一个标签页包含多个iframe标签且他们属于同源页面，那么他们之间是可以共享sessionStorage的。

三.基于浏览器的缓存协商（last-modified,expries,cache-control,Etag）

这个在我一篇文章有详细的描述

#### CDN缓存

> 浏览器缓存只是为了提升页面再次被访问的速度，而对于提升首次访问的响应能力，通常是采用CDN进行加速。CDN在前端优化过程中起着关键性的作用，理解CDN的工作原理对前端开发人员提升网站性能有着很大的帮助。

什么是CDN？

CDN的全称是Content Delivery Network，即内容分发网络，是指一种通过互联网互相连接的电脑网络系统，利用最靠近每位用户的服务器，更快、更可靠地将音乐、图片、视频、应用程序及其他文件发送给用户，来提供高性能、可扩展性及低成本的网络内容传递给用户。————维基百科

使用CDN好处？

- 提升网页加载速度
- 处理高流量负载
- 减少带宽消耗
- 在多台服务器间均衡负载
- 使你的网站免于DDoS（拒绝服务）的攻击


原理：

用户在通过浏览器访问未使用CDN加速的网站的流程：

![图1](https://img-blog.csdnimg.cn/20201023113704716.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI0MDczODg1,size_16,color_FFFFFF,t_70#pic_center)


 - 用户在浏览器中输入要访问的域名。
 - 浏览器向DNS服务器请求对该域名的解析。
 - DNS服务器返回该域名的IP地址给浏览器。
 - 浏览器使用该IP地址向服务器请求内容。
 - 服务器将用户请求的内容返回给浏览器。
 
用户在通过浏览器访问使用CDN加速的网站的流程：

![图2](https://img-blog.csdnimg.cn/2020102311371680.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI0MDczODg1,size_16,color_FFFFFF,t_70#pic_center)


 - 用户在浏览器中输入要访问的域名。
 - **浏览器向DNS服务器请求对域名进行解析。由于CDN对域名解析进行了调整，DNS服务器会最终将域名的解析权交给CNAME指向的CDN专用DNS服务器。**
 - **CDN的DNS服务器将CDN的负载均衡设备IP地址返回给用户。**
 - **用户向CDN的负载均衡设备发起内容URL访问请求。**
 - **负载均衡设备根据算法为用户选择合适的服务器。**
 - 浏览器使用该IP地址向服务器请求内容。
 - 服务器将用户请求的内容返回给浏览器。
 
总结：

CDN可以加速用户访问速度，减少源站中心负载压力。  
CDN建议还是用云服务商的（七牛云之类的），又便宜，又不用管cdn的算法和负载均衡。还能节省服务器成本和维护开支

#### 代理服务器缓存

nginx代理缓存（proxy_cache）

A（用户1）请求服务器资源，
proxy_cache将A从服务器资源上获取到的数据根据预设规则存放到B（内存+磁盘）上留着备用，
C（用户2）请求服务器时，服务器会把缓存的B里的数据直接给C，而不需要再去向服务器去获取。

proxy_cache有几个参数设置要注意下：

- proxy_cache (开启关闭缓存,需要注意的是从nginx 0.7.66版本开始，proxy_cache机制开启后会检测被代理端的HTTP响应头中的"Cache-Control"、"Expire"头域。
如，Cache-Control为no-cache时，是不会缓存数据的)


- proxy_cache_bypass（该参数设定，什么情况下的请求不读取cache而是直接从后端的服务器上获取资源）
- proxy_cache_path（设置缓存存放路径）


#### 数据库缓存

开启mysql缓存（query cache ）


query cache 是用来缓存我们所执行的SELECT语句以及该语句的结果集

参数：

 - query_cache_limit：允许缓存的单条查询结果集的最大容量，默认是1MB，超过此参数设置的查询结果集将不会被缓存
 - query_cache_min_res_unit：设置查询缓存Query Cache每次分配内存的最小空间大小，即每个查询的缓存最小占用的内存空间大小
 - query_cache_size：设置 Query Cache 所使用的内存大小，默认值为0，大小必须是1024的整数倍，如果不是整数倍，MySQL 会自动调整降低最小量以达到1024的倍数
 - query_cache_type：控制 Query Cache 功能的开关 （0，1，2） 1是开启
 - query_cache_wlock_invalidate：控制当有写锁定发生在表上的时刻是否先失效该表相关的Query Cache
 

#### 外部缓存

Redis缓存

Redis是一款内存高速缓存数据库

作用：提高网站效率，降低数据库的读写次数，引入缓存技术

本质：缓存就是内存中存储数据备份，当数据没有发生本质改变的时候不去数据库中查询，而是去内存中取数据

有两种类别：
- 页面缓存
- 数据缓存


Redis有丰富的数据结构，一边运行，一边将数据备份到硬盘，防止断电等意外情况


以上5种大概可以囊括基本的web缓存架构，学会了吗
    
       
--- 