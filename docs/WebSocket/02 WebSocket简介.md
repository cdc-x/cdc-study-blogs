# WebSocket 简介

## 一、WebSocket协议

WebSocket 协议是基于 TCP 的一种新的协议。WebSocket 最初在 HTML5 规范中被引用为 TCP 连接，作为基于 TCP 的套接字 API 的占位符。它实现了浏览器与服务器全双工（full-duplex）通信。其本质是保持 TCP 连接，在浏览器和服务端通过 Socket 进行通信（简单理解，就是 Web 版的 Socket 通信）。

由于 WebSocket 协议的特点，该技术常常用于页面聊天（弹幕）、大屏数据展示、图表数据监测等对数据实时性要求较高的场景。

## 二、WebSocket原理

### 1. 请求过程

传统的 `HTTP` 协议下，客户端和服务端的一次请求过程，大体可以分为几个步骤：

- 客户端建立连接
- 客户端发送请求，服务器端返回数据
- 客户端接收到数据
- 客户端断开连接

而基于 `HTTPS` 协议的请求过程和 `HTTP` 协议是一样的，但在传输数据时进行了 `SSL` 加密。

`websocket` 协议其实也是建立在 `HTTP` 协议之上的，但其请求过程多了一些处理：

- 建立连接
- 握手（又称验证），这一步主要是客户端为了确认请求的目标服务端是否支持 `websocket` 通信
- 客户端与服务端收发数据（数据是加密的）
- 断开连接

### 2. 握手

`websocket` 协议**握手**主要由以下几个步骤实现：

- 客户端向服务端发送请求，请求头中会包含一个随机生成的字符串（Sec-WebSocket-Key）

```
GET /chatsocket HTTP/1.1
Host: 127.0.0.1:8002
Connection: Upgrade
Pragma: no-cache
Cache-Control: no-cache
Upgrade: websocket
Origin: http://localhost:63342
Sec-WebSocket-Version: 13
Sec-WebSocket-Key: mnwFxiOlctXFN/DeMt1Amg==
Sec-WebSocket-Extensions: permessage-deflate; client_max_window_bits
...
...
\r\n\r\n
```

- 服务端会提取出随机字符串，然后进行以下操作

```python
# 第一步，提取出随机字符串
key = "mnwFxiOlctXFN/DeMt1Amg=="

# 第二步，将随机字符串和 magic string 进行拼接，得到一个新的字符串
# magic string为：258EAFA5-E914-47DA-95CA-C5AB0DC85B11
new_key = key + "258EAFA5-E914-47DA-95CA-C5AB0DC85B11"

# 第三步，将新生成的字符串进行 hmac1 加密
v1 = hmac1(new_key)

# 第四步，将 v1 再进行 base64 加密
v2 = base64(v1)

# 第五步，服务端将最后加密完成的数据，放在响应头中给客户端返回
HTTP/1.1 101 Switching Protocols
Upgrade:websocket
Connection: Upgrade
Sec-WebSocket-Accept:v2
```

- 客户端接收到响应之后，会提取出服务端返回的加密后的字符串内容。客户端针对生成的随机字符串，其内部也有一套加密的流程，将内部加密后的内容与服务端返回的加密的内容进行对比，如果两者一致，则表示服务端支持 `websocket` 协议，握手通过，可以进行数据收发。

### 3. 数据解密

基于 `websocket` 协议通信时，客户端与服务端收发的数据，底层都会自动加密。官方针对加密和解密的方式也给出了详细说明：

```
0                   1                   2                   3
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
+-+-+-+-+-------+-+-------------+-------------------------------+
|F|R|R|R| opcode|M| Payload len |    Extended payload length    |
|I|S|S|S|  (4)  |A|     (7)     |             (16/64)           |
|N|V|V|V|       |S|             |   (if payload len==126/127)   |
| |1|2|3|       |K|             |                               |
+-+-+-+-+-------+-+-------------+ - - - - - - - - - - - - - - - +
|     Extended payload length continued, if payload len == 127  |
+ - - - - - - - - - - - - - - - +-------------------------------+
|                               |Masking-key, if MASK set to 1  |
+-------------------------------+-------------------------------+
| Masking-key (continued)       |          Payload Data         |
+-------------------------------- - - - - - - - - - - - - - - - +
:                     Payload Data continued ...                :
+ - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - +
|                     Payload Data continued ...                |
+---------------------------------------------------------------+
```

> The MASK bit simply tells whether the message is encoded. Messages from the client must be masked, so your server should expect this to be 1. (In fact, [section 5.1 of the spec](http://tools.ietf.org/html/rfc6455#section-5.1) says that your server must disconnect from a client if that client sends an unmasked message.) When sending a frame back to the client, do not mask it and do not set the mask bit. We'll explain masking later. *Note: You have to mask messages even when using a secure socket.*RSV1-3 can be ignored, they are for extensions.
>
> The opcode field defines how to interpret the payload data: 0x0 for continuation, `0x1` for text (which is always encoded in UTF-8), `0x2` for binary, and other so-called "control codes" that will be discussed later. In this version of WebSockets, `0x3` to `0x7` and `0xB` to `0xF` have no meaning.
>
> The FIN bit tells whether this is the last message in a series. If it's 0, then the server will keep listening for more parts of the message; otherwise, the server should consider the message delivered. More on this later.
>
> **Decoding Payload Length**
>
> To read the payload data, you must know when to stop reading. That's why the payload length is important to know. Unfortunately, this is somewhat complicated. To read it, follow these steps:
>
> 1. Read bits 9-15 (inclusive) and interpret that as an unsigned integer. If it's 125 or less, then that's the length; you're **done**. If it's 126, go to step 2. If it's 127, go to step 3.
> 2. Read the next 16 bits and interpret those as an unsigned integer. You're **done**.
> 3. Read the next 64 bits and interpret those as an unsigned integer (The most significant bit MUST be 0). You're **done**.
>
> **Reading and Unmasking the Data**
>
> If the MASK bit was set (and it should be, for client-to-server messages), read the next 4 octets (32 bits); this is the masking key. Once the payload length and masking key is decoded, you can go ahead and read that number of bytes from the socket. Let's call the data **ENCODED**, and the key **MASK**. To get **DECODED**, loop through the octets (bytes a.k.a. characters for text data) of **ENCODED** and XOR the octet with the (i modulo 4)th octet of MASK. In pseudo-code (that happens to be valid JavaScript):
>
>  
>
> var DECODED = "";
> for (var i = 0; i < ENCODED.length; i++) {
>   DECODED[i] = ENCODED[i] ^ MASK[i % 4];
> }
>
>  
>
> Now you can figure out what **DECODED** means depending on your application.

其只要解密流程如下：

- 对于接收到的加密数据，取出第二个字节（一个字节8位），如：10001010
- 取该字节的后 7 位（如：0001010），根据后 7 位的长度，对数据进行切分（除剩余字节，其他的字节为数据头）
  - 长度 = 127，数据分为：2字节 + 8字节 + 剩余字节
  - 长度 = 126，数据分为：2字节 + 2字节 + 剩余字节
  - 长度 <= 125，数据分为：2字节  + 剩余字节
- 对剩余字节，前 4 个字节（32位）为掩码（masking key），最终剩余的部分才是真正的数据部分
- 数据根据掩码进行解密

```javascript
var DECODED = "";

// 取每一个字节
// 将字节的每一位和掩码对应的每一位进行运算
for (var i = 0; i < ENCODED.length; i++) {
  DECODED[i] = ENCODED[i] ^ MASK[i % 4];
}
```

