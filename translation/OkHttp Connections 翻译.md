# OkHttp Connections 翻译

# Connections

Although you provide only the URL, OkHttp plans its connection to your webserver using three types: URL, Address, and Route.

## URLs

URLs (like https://github.com/square/okhttp) are fundamental to HTTP and the Internet. In addition to being a universal(普遍), decentralized(分散) naming scheme for everything on the web, they also specify how to access web resources.

URLs are abstract:

They specify that the call may be plaintext(纯文本) (http) or encrypted(加密的) (https), but not which cryptographic algorithms（加密算法） should be used. Nor do they specify how to verify the peer's certificates (the HostnameVerifier) or which certificates can be trusted (the SSLSocketFactory).
They don't specify whether a specific proxy server should be used or how to authenticate with that proxy server.

They're also concrete（具体的）: each URL identifies a specific path (like /square/okhttp) and query (like ?q=sharks&lang=en). Each webserver hosts many URLs.

## Addresses

Addresses 确定了一个webserver（ru github.com）并且所有必要的连接到服务器的静态配置：端口号，HTTPS设置，和更喜欢的网络协议（如 HTTP/2 or SPDY）

分享同一个地址的URLs可能也分享同一个底层TCP socket connection.分享同一个connection有大量的好处：更低的延迟，更高的吞吐量（因为TCP slow start）和更低的电量消耗。OkHttp使用ConnectionPool自动重用HTTP/1.x的connections和multiplexes HTTP/2和SPDY connections

在OkHttp，address的一些字段来自URL（scheme，hostname，port），其余的来自OkHttpClient

## Routes

Routes提供了动态的必要信息来连接到webserver。这包括要链接的唯一的IP地址（通过DNS查询到的），真实的代理服务器（如果在使用ProxySelector），使用那个版本的TLS来negotiate(妥协)（为了HTTPS连接）

对一个单独的address可能有多个routes。例如，被多个数据中心持有的一个webserver可能在DNS response产生多个IP地址。

## Connections

当你使用OkHttp请求一个URL，会做如下：

 1. 它使用URL和配置好的OkHttpClient来创建一个address。这个地址指定了我们将怎样连接到webserver
 2. 它尝试使用address从connection pool拿到一个connection
 3. 如果他没有在pool中找到connection，它会选择了一个route来尝试。这常常意味着做一个DNS请求来拿到server的IP addresses。如果必要的话，它然后选择一个TLS版本和代理服务器。
 4. 如果是一个新的route，它通过一个直接的socket connection，a TLS tunnel(for HTTPS over an HTTP proxy),或者一个直接的TLS connection.它在必要的时候做TLS handshakes。
 5. 它发送HTTP请求并且读取response
 
 如果connection有问题，OkHttp将选择另一个route并且再次尝试。当一个服务器的地址的子集无法到的的时候，这允许OkHttp恢复。当一个pooled connection 陈旧了或者已尝试的TLS 版本不支持了，这也是有用的。
 一旦response已经被取回了，这个连接将返回到pool中，所以它可以在一个将来的request中被重用。在一段时间不活跃后，Connection会被从池中移除。