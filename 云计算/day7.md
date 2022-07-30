# day7

## 访问网站的基本流程

* 客户端，在浏览器中输入网址 www.baidu.com,回车
* 客户端，DNS域名解析服务，将网址转换成ip地址（将多个ip地址与一个域名关联）
* DNS客户端缓存（浏览器缓存< 系统缓存 < 路由器缓存)
* ISP(运营商)DNS缓存
* 根域名服务器
* 顶级域名服务器
* 主机名服务器
* 保存结果至缓存
* 客户端，直接访问响应网址服务器，建立三次握手过程
* 客户端，发送HTTP请求报文
* 服务端，发送HTTP响应报文
* 客户端，浏览器查看网页内容，html解析
* 客户端，结束网站访问

```
linux中系统缓存
/etc/hosts
windows系统缓存
C:\Windows\System32\drivers\etc
```

## HTTP协议

* HTTP协议，全称HyperText Transfer Protocol，中文名为超文本传输协议，是互联网中最常用的
  一种网络协议。HTTP重要应用之一是www服务。涉及HTTP协议最初的目的就是提供一种发布和接
  受HTML页面的方法。HTTP协议是互联网上最常用的通信协议之一，它有很多的应用，但最流行的
  就是用于web浏览器和web服务器之间的通信，即www应用或称web应用
* HTTP是一个基于TCP/IP通信协议来传递数据（HTML 文件,图片文件, 查询结果等）的应用层协议
* HTTP协议工作于C/S或B/S架构。浏览器作为HTTP客户端通过URL向HTTP服务端即WEB服务器发
  送所有请求。 Web服务器根据接收到的请求后，向客户端发送响应信息。

## URL和URI

* 统一资源标志符URI就是在某一规则下能把一个资源独一无二地标识出来。
* URL是一种特殊类型的URI，全称是UniformResourceLocator(统一资源定位符),是互联网上用来标
  识某一处资源的 地址。

## HTTP请求报文

```
GET /hello.htm HTTP/1.1
User-Agent: Mozilla/4.0 (compatible; MSIE5.01; Windows NT)
Host: www.tutorialspoint.com
Accept-Language: en-us
Accept-Encoding: gzip, deflate
Connection: Keep-Alive(长连接)
1.1版本功能
```

## HTTP响应报文

```
HTTP/1.1 200 OK
Date: Mon, 27 Jul 2009 12:28:53 GMT
Server: Apache/2.2.14 (Win32)
Last-Modified: Wed, 22 Jul 2009 19:15:56 GMT
Content-Length: 88
Content-Type: text/html
Connection: Closed
200 状态码
```

## HTTP请求报文包含

请求行

```
请求方法
    GET：请求指定的页面信息
    HEAD：类似get请求，但是返回仅仅是头部信息，一个使用场景是在下载一个大文件前
    先获取其大小再决定是否要下载, 以此可以节约带宽资源.
    POST：向指定资源提交数据进行处理请求（例如提交表单或者上传文件）
    PUT：从客户端向服务器传送的数据取代指定的文档内容
    DELETE：请求服务器删除指定的页面
    CONNECT： 预留给能够将连接改为管道方式的代理服务器
    OPTIONS：允许客户端查看服务器的性能
    TRACE： 回显服务器收到的请求，主要用于测试或诊断!
请求信息
	index.html
请求协议
    HTTP0.9：仅支持GET方法，仅能访问HTML格式的资源
    HTTP1.0：增加POST和HEAD方法，MIME支持多种数据格式，开始支持Cache，支持tcp短连接
    HTTP1.1：支持持久连接（长连接），一个TCP连接允许多个请求，新增PUT、PATCH、DELETE等
    HTTP2.0：性能大幅提升，新的二进制格式，多路复用，header压缩，服务端推送。	
```

### GET和POST

1.GET提交的数据会放在URL之后，以?分割URL和传输数据，参数之间以&相连，EditPosts.aspx？name=test1&id=123456 POST方法是把提交的数据放在HTTP包的Body中。 

2.GET提交的数据大小有限制（因为浏览器对URL的长度有限制），而POST方法提交的数据没有限制。

3.GET方式需要使用Request.QueryString 来取得变量的值，而POST方式通过 Request.Form 来获取变量 的值。 

4.GET方式提交数据，会带来安全问题，比如一个登录页面，通过GET方式提交数据时，用户名和密码将出现在URL 上，如果页面可以被缓存或者其他人可以访问这台机器，就可以从历史记录获得该用户的账号和密码。

请求头

```
客户端有关信息介绍说明
```

请求体

```
使用get方法时，没有请求主体
使用post方法时，有请求主体信息
```

### 响应报文包含

* 起始行
  * 协议版本
  * 状态码
    * 1xx：指示信息——表示请求已经接收，继续处理、
    * 2xx：成功——表示请求已经被成功接收、理解、接收
    * 3xx：重定向——要完成请求必须进行更进一步的操作
    * 4xx：客户端错误——请求的语法有错误或请求无法实现
    * 5xx：服务端错误——服务器未能实现合法的请求!
    * 200：OK请求已经正常处理完毕
    * 301：请求永久重定向
    * 302：请求临时重定向
    * 304：请求被重定向到客户端本地缓存
    * 400：客户端请求存在语法错误
    * 401：客户端请求没有经过授权
    * 403：客户端的请求被服务器拒绝，一般为客户端没有访问权限
    * 404：客户端请求的URL在服务端不存在
    * 408：遇到408意味着你的请求发送到该网站花的时间比该网站的服务器准备等待的时间要长，即链接超时。
    * 500：服务端永久错误
    * 503：服务端发生临时错误
  * 响应头部
  * 空行
  * 响应主体