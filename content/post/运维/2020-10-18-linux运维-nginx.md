+++
title="Linux运维 实战（八）| nginx"
tags=["Linux运维"]
categories=["Linux运维"]
date="2020-18-28T13:00:00+08:00"
+++

### 3.Nginx的超时timeout配置详解
- Nginx 处理的每个请求均有相应的超时设置

#### 3.1 keepalive_timeout
```
1. HTTP 是一种无状态协议，客户端向服务器发送一个 TCP 请求，服务端响应完毕后断开连接。
2. 客户端向服务器发送多个请求，每个请求都要建立各自独立的连接以传输数据。
3. HTTP 有一个 KeepAlive 模式，它告诉 webserver 在处理完一个请求后保持这个 TCP 连接的打开状态。若接收到来自客户端的其它请求，服务端会利用这个未被关闭的连接，而不需要再建立一个连接。KeepAlive 在一段时间内保持打开状态，它们会在这段时间内占用资源。占用过多就会影响性能。
4. Nginx 使用 keepalive_timeout 来指定 KeepAlive 的超时时间（timeout）。指定每个 TCP 连接最多可以保持多长时间。Nginx 的默认值是 75 秒，有些浏览器最多只保持 60 秒，所以可以设定为 60 秒。若将它设置为 0，就禁止了 keepalive 连接。
# 配置段: http, server, location
  keepalive_timeout 60s;
```

#### 3.2 client_body_timeout
```
1. 客户端与服务端建立连接后发送 request body 的超时时间
2. 如果客户端在指定时间内没有发送任何内容，Nginx 返回 HTTP 408（Request Timed Out）。
# 配置段: http, server, location
client_body_timeout 20s;
```

#### 3.3 client_header_timeout
```
1. 客户端向服务端发送一个完整的 request header 的超时时间。
2. 如果客户端在指定时间内没有发送一个完整的 request header，Nginx 返回 HTTP 408（Request Timed Out）。
# 配置段: http, server, location
client_header_timeout 10s;
```
#### 3.4 send_timeout
```
1. 服务端向客户端传输数据的超时时间。
# 配置段 : http, server, location
send_timeout 30 s;
客户度连接nginx超时, 建议5s内
接收客户端header超时, 默认60s, 如果60s内没有收到完整的http包头, 返回408
```
Syntax: client_header_timeout time;
Default:
client_header_timeout 60s;
Context: http, server
Defines a timeout for reading client request header. If a client does not transmit the entire header within this time,
the 408 (Request Time-out) error is returned to the client.
接收客户端body超时, 默认60s, 如果连续的60s内没有收到客户端的1个字节, 返回408

Syntax: client_body_timeout time;
Default:
client_body_timeout 60s;
Context: http, server, location
Defines a timeout for reading client request body. The timeout is set only for a period between two successive read operations, not for the transmission of the whole request body.
If a client does not transmit anything within this time,
the 408 (Request Time-out) error is returned to the client.
keepalive时间，默认75s，通常keepalive_timeout应该比client_body_timeout大

Syntax: keepalive_timeout timeout [header_timeout];
Default:
keepalive_timeout 75s;
Context: http, server, location
The first parameter sets a timeout during which a keep-alive client connection will stay open on the server side. The zero value disables keep-alive client connections.
The optional second parameter sets a value in the “Keep-Alive: timeout=time” response header field. Two parameters may differ.
The “Keep-Alive: timeout=time” header field is recognized by Mozilla and Konqueror. MSIE closes keep-alive connections by itself in about 60 seconds.

可以理解为TCP连接关闭时的SO_LINGER延时设置，默认5s

Syntax: lingering_timeout time;
Default:
lingering_timeout 5s;
Context: http, server, location
When lingering_close is in effect, this directive specifies the maximum waiting time for more client data to arrive. If data are not received during this time,
the connection is closed. Otherwise, the data are read and ignored, and nginx starts waiting for more data again.
The “wait-read-ignore” cycle is repeated, but no longer than specified by the lingering_time directive.
域名解析超时，默认30s

Syntax: resolver_timeout time;
Default:
resolver_timeout 30s;
Context: http, server, location
Sets a timeout for name resolution, for example:
resolver_timeout 5s;
发送数据至客户端超时, 默认60s, 如果连续的60s内客户端没有收到1个字节, 连接关闭

Syntax: send_timeout time;
Default:
send_timeout 60s;
Context: http, server, location
Sets a timeout for transmitting a response to the client. The timeout is set only between two successive write operations,
not for the transmission of the whole response. If the client does not receive anything within this time, the connection is closed.
nginx与upstream server的连接超时时间

Syntax: proxy_connect_timeout time;
Default:
proxy_connect_timeout 60s;
Context: http, server, location
Defines a timeout for establishing a connection with a proxied server. It should be noted that this timeout cannot usually exceed 75 seconds.
nginx接收upstream server数据超时, 默认60s, 如果连续的60s内没有收到1个字节, 连接关闭

Syntax: proxy_read_timeout time;
Default:
proxy_read_timeout 60s;
Context: http, server, location
Defines a timeout for reading a response from the proxied server. The timeout is set only between two successive read operations,
not for the transmission of the whole response. If the proxied server does not transmit anything within this time, the connection is closed.
nginx发送数据至upstream server超时, 默认60s, 如果连续的60s内没有发送1个字节, 连接关闭

Syntax: proxy_send_timeout time;
Default:
proxy_send_timeout 60s;
Context: http, server, location
Sets a timeout for transmitting a request to the proxied server. The timeout is set only between two successive write operations,
not for the transmission of the whole request. If the proxied server does not receive anything within this time, the connection is closed.