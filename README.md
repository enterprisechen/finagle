finagle
=======

finagle框架的中文教程

根据我的理解，自己翻译的finagle框架 中文教程，原文在https://github.com/twitter/finagle。

我将从下面几点描述Finagle 框架
1. Finagle 是什么
2.Finagle  用来干啥的
3.我们为什么要使用Finagle
4.Finagle 框架的架构
5.如何使用Finagle


现在就开始Finagle 框架之旅吧 ！ 

Finagle 是基于JVM平台上的异步的网络框架，你可以用它来构建异步的RPC(Remote Procedure Call) 客户端和服务端，在JVM平台上语言都可以使用它 特别是Java,scala 我这里主要用scala 作为例子的语言。因为我开始喜欢上了scala. 下面我们用一个简单的例子来描述Finagle
用scala 实现一个简单的 HTTP 服务端：
 
val service: Service[HttpRequest, HttpResponse] = new Service[HttpRequest, HttpResponse] { // 1
  def apply(request: HttpRequest) = Future(new DefaultHttpResponse(HTTP_1_1, OK))            // 2
}

val address: SocketAddress = new InetSocketAddress(10000)                                  // 3

val server: Server = ServerBuilder()                                                       // 4
  .codec(Http())
  .bindTo(address)
  .name("HttpServer")
  .build(service)

下面对这段代码作一下说明：
1 创建一个处理请求和响应的服务
2. 对于一个请求，异步的响应一个 200 OK ，Future实例是一个异步操作的抽象。
3. 设置本地SocketAddress的端口 10000.访问的时候就可以用http://localhost:10000
4.配置一个服务器来响应Http请求了 需要下面这些参数
    1)  一个 Http codec 这是确保只有一个正确的Http 请求能够被服务器接收到
    2) 本地socket 监听请求
    3) 一个处理请求的服务
    4) 这个服务的名称

同样的 我们来构建一个简单客户端：

val client: Service[HttpRequest, HttpResponse] = ClientBuilder()                           // 1
  .codec(Http())
  .hosts(address)
  .hostConnectionLimit(1)
  .build()

// Issue a request, get a response:
val request: HttpRequest = new DefaultHttpRequest(HTTP_1_1, GET, "/")                      // 2
val responseFuture: Future[HttpResponse] = client(request)                                 // 3
  onSuccess { response => println("Received response: " + response)                        // 4
  }

同样的我们对这段代码作一个介绍
1.创建一个客户端client，用来发送Http 请求。
2.创建一个HttpRequest实例
3.用一个回调函数来处理请求成功的链接，这是异步处理的。这里也用到了Future实例。

到这里，我想对于Finagle框架应该有了一个简单的认识，应该知道Finagle 框架是什么了。下面我们主要讲Finagle 框架是具体干啥的，我为啥要用Finagle 了