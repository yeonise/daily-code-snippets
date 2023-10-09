# WebSockets

This part of the reference documentation covers support for Servlet stack, WebSocket messaging that includes raw WebSocket interactions, WebSocket emulation through SockJS, and publish-subscribe messaging through STOMP as a sub-protocol over WebSocket.

> 공식문서의 이 부분에서는 Servlet stack, 순수한 웹소켓 상호작용을 포함하는 웹 소켓 메시징, SockJS를 사용한 웹 소켓 에뮬레이션, 그리고 STOMP를 사용한 발행-구독 메시징에 대한 지원을 다룹니다. 

<br>

## Introduction to WebSocket

> 웹소켓 개요

<br>

The WebSocket protocol, RFC 6455, provides a standardized way to establish a full-duplex, two-way communication channel between client and server over a single TCP connection.
It is a different TCP protocol from HTTP but is designed to work over HTTP, using ports 80 and 443 and allowing re-use of existing firewall rules.

> RFC 6455에 정의된 웹소켓 프로토콜은 단일 TCP 연결을 통해 클라이언트와 서버사이의 전이중 양방향 통신 채널을 설정하는 표준화된 방법을 제공합니다.
> HTTP 프로토콜과는 다른 TCP 프로토콜이지만 HTTP 위에서 동작하도록 디자인되어 있으며, 80 혹은 443 포트를 사용하고 기존 방화벽 규칙을 재사용할 수 있습니다.

<br>

A WebSocket interaction begins with an HTTP request that uses the HTTP Upgrade header to upgrade or, in this case, to switch to the WebSocket protocol.
The following example shows such an interaction:

```yaml
GET /spring-websocket-portfolio/portfolio HTTP/1.1
Host: localhost:8080
Upgrade: websocket          ... (1)
Connection: Upgrade         ... (2)
Sec-WebSocket-Key: Uc9l9TMkWGbHFD2qnFHltg==
Sec-WebSocket-Protocol: v10.stomp, v11.stomp
Sec-WebSocket-Version: 13
Origin: http://localhost:8080
```
1. The Upgrade header.
2. Using the Upgrade connection.


> 웹소켓 상호작용은 HTTP 업그레이드 헤더를 사용하여 업그레이드 하거나, 웹소켓 프로토콜로 전환하는 HTTP 요청으로부터 시작됩니다.
> 다음의 예제는 상호작용 예제를 보여줍니다.
>```yaml
>GET /spring-websocket-portfolio/portfolio HTTP/1.1
>Host: localhost:8080
>Upgrade: websocket             ... (1)
>Connection: Upgrade            ... (2)
>Sec-WebSocket-Key: Uc9l9TMkWGbHFD2qnFHltg==
>Sec-WebSocket-Protocol: v10.stomp, v11.stomp
>Sec-WebSocket-Version: 13
>Origin: http://localhost:8080
>```
> 1. 업그레이드 헤더
> 2. 업그레이드 커넥션 사용

<br>

Instead of the usual 200 status code, a server with WebSocket support returns output similar to the following:
```yaml
HTTP/1.1 101 Switching Protocols
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Accept: 1qVdfYHU9hPOl4JYYNXF623Gzn0=
Sec-WebSocket-Protocol: v10.stomp
```

> 웹소켓을 지원하는 서버는 일반적인 200 상태 코드 대신에, 다음과 유사한 결과물을 반환합니다.
> ```yaml
> HTTP/1.1 101 Switching Protocols      ... (1)
> Upgrade: websocket
> Connection: Upgrade
> Sec-WebSocket-Accept: 1qVdfYHU9hPOl4JYYNXF623Gzn0=
> Sec-WebSocket-Protocol: v10.stomp
> ```
> 1. 프로토콜 전환

<br>

After a successful handshake, the TCP socket underlying the HTTP upgrade request remains open for both the client and the server to continue to send and receive messages.

A complete introduction of how WebSockets work is beyond the scope of this document. 
See RFC 6455, the WebSocket chapter of HTML5, or any of the many introductions and tutorials on the Web.

Note that, if a WebSocket server is running behind a web server (e.g. nginx), you likely need to configure it to pass WebSocket upgrade requests on to the WebSocket server.
Likewise, if the application runs in a cloud environment, check the instructions of the cloud provider related to WebSocket support.

> 성공적인 핸드쉐이킹 이후, HTTP 업그레이드 요청에 기반이 되는 TCP 소켓은 클라이언트와 서버모두 메시지를 계속 주고 받을 수 있도록 열려 있습니다.
> 
> 웹소켓이 어떻게 동작하는지에 대한 완벽한 설명은 공식문서의 범위를 벗어납니다.
> RFC 6455의 HTML5 웹소켓 챕터를 보거나, 인터넷에 있는 설명이나 튜토리얼들을 참고하십시오.
> 
> 만약 웹소켓 서버가 nginx와 같은 정적 웹 서버 뒤에서 돌아간다면, 웹소켓 업그레이드 요청이 정적 웹 서버를 통과할 수 있도록 설정해야 합니다.
> 마찬가지로 애플리케이션이 클라우드 환경에서 실행된다면, 웹소켓 지원과 관련된 클라우드 제공업체의 지침을 확인하십시오.

<br>

## HTTP Versus WebSocket
