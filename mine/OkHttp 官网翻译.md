# OkHttp
翻译文 [链接](https://square.github.io/okhttp/)

An HTTP & HTTP/2 client for Android and Java applications

## Overview

HTTP是现代应用的网络方式。他帮助交换数据和媒体信息，有效率地使用HTTP是你的事物加载更快并且save bandwidth（节省带宽）。OkHttp是一个有效率的客户端，因为：

* HTTP/2支持同一个host的所有请求分享一个socket
* Connecting pooling 减少了请求泄漏（如果HTTP/2拿不到）
* Transparent GZIP缩小了下载尺寸
* 响应缓存完全避免了重复的网络请求

当网络状况糟糕时，OkHttp依然坚持不懈。它可以从常见的连接问题中恢复。如果你的服务有多个IP地址，如果第一个连接失败了，OkHttp将尝试备用地址。在IPv4+IPv6和服务被冗余的数据中心持有的情况下，这种尝试很有必要。OkHttp支持现代TLS特征。为了广泛的连接，它可能被配置为回退。
使用OkHttp很简单，他的request/reponse API都被设计为流畅的建造者和不可变的。它支持同步的阻塞调用和异步调用、回调。
OkHttp支持Android 5.0+（API 21+）和 Java 8+