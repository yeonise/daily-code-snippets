# Hypertext Transfer Protocol -- HTTP/1.1

**Status of this Memo**

This document specifies an Internet standards track protocol for the
Internet community, and requests discussion and suggestions for
improvements. <br>
Please refer to the current edition of the "Internet
Official Protocol Standards" (STD 1) for the standardization state
and status of this protocol.  Distribution of this memo is unlimited.

>**메모의 상태**
> 
> 이 문서는 인터넷 표준 추적 프로토콜을 명세하고, 이를 개선할 토의나 제안을 요청합니다. <br>
> 이 프로토콜의 표준화 및 상태에 대해서는 STD1 의 최신 버전을 참고하십시오.
> 이 메모는 언제든지 배포할 수 있습니다.

<br>

**Copyright Notice**

Copyright (C) The Internet Society (1999).  All Rights Reserved.

> **저작권 공지**
>
> The Internet Society 가 모든 저작권을 소유합니다.

<br>

**Abstract**

The Hypertext Transfer Protocol (HTTP) is an application-level
protocol for distributed, collaborative, hypermedia information
systems. <br>
It is a generic, stateless, protocol which can be used for
many tasks beyond its use for hypertext, such as name servers and
distributed object management systems, through extension of its
request methods, error codes and headers. <br>
A feature of HTTP is the typing and negotiation of data representation, allowing systems
to be built independently of the data being transferred. <br>

HTTP has been in use by the World-Wide Web global information initiative since 1990. <br>
This specification defines the protocol referred to as "HTTP/1.1", and is an update to RFC 2068.

>**요약**
> 
> HTTP 는 분산, 협업, 하이퍼미디어 정보 시스템을 위한 애플리케이션 계층의 프로토콜입니다. <br>
> HTTP 는 일반적이고 무상태의 프로토콜 입니다. - 이것은 하이퍼 텍스트 이외에도 네임 서버와 분산 객체 관리 시스템과 같은
> 다양한 용도로도 사용됩니다.<br>
> HTTP의 기능은 데이터 표현의 입력 및 협상으로, 시스템들이 전송되는 데이터로부터 독립적으로 구축될 수 있게 합니다.
>
> HTTP는 1990년 `World-Wide Web global information initiative` 에서 사용되었습니다. <br>
> 이 스펙은 HTTP/1.1 프로토콜을 정의하며, 이는 RFC 2068의 업데이트 버전입니다.

<br>

## 1. Introduction
### 1.1 Purpose

The Hypertext Transfer Protocol (HTTP) is an application-level
protocol for distributed, collaborative, hypermedia information
systems. <br>
HTTP has been in use by the World-Wide Web global information initiative since 1990. <br>
The first version of HTTP, referred to as HTTP/0.9, was a simple protocol for raw data transfer across the Internet.<br>
HTTP/1.0, as defined by RFC 1945, improved the protocol by allowing messages to be in the format of MIME-like
messages, containing metainformation about the data transferred and modifiers on the request/response semantics. <br>
However, HTTP/1.0 does not sufficiently take into consideration the effects of hierarchical proxies, caching, the need for persistent connections, or virtual hosts. <br>
In addition, the proliferation of incompletely-implemented applications calling themselves "HTTP/1.0" has necessitated a
protocol version change in order for two communicating applications to determine each other's true capabilities. <br>

This specification defines the protocol referred to as "HTTP/1.1". <br>
This protocol includes more stringent requirements than HTTP/1.0 in order to ensure reliable implementation of its features. <br>

Practical information systems require more functionality than simple retrieval, including search, front-end update, and annotation. <br>
HTTP allows an open-ended set of methods and headers that indicate the purpose of a request. <br>
It builds on the discipline of reference provided by the Uniform Resource Identifier (URI), as a location
(URL) or name (URN), for indicating the resource to which a method is to be applied. <br>
Messages are passed in a format similar to that used by Internet mail as defined by the Multipurpose Internet Mail Extensions (MIME). <br>

HTTP is also used as a generic protocol for communication between user agents and proxies/gateways to other Internet systems, including
those supported by the SMTP, NNTP, FTP, Gopher, and WAIS protocols. <br>
In this way, HTTP allows basic hypermedia access to resources available from diverse applications.<br>

> HTTP 는 분산, 협업, 하이퍼미디어 정보를 위한 애플리케이션 계층 프로토콜입니다. <br>
> HTTP는 1990년 `World-Wide Web global information initiative` 에서 처음 사용되었습니다. <br>
> HTTP 0.9 로 일컬어지는 첫 번째 버전의 HTTP는 인터넷에서 raw data를 주고받는 간단한 프로토콜이었습니다. <br>
> RFC 1945에 해당하는 HTTP 1.0 버전부터는, 전송데이터의 메타정보와 수정자를 포함하는 MIME 메시지 형식을 주고받음으로써 프로토콜을 개선했습니다. <br>
> 그러나 HTTP 1.0은 계층적인 프록시, 캐싱, 지속적인 연결과 가상 호스트를 충분히 고려하지 않습니다. <br>
> 또한, 스스로를 "HTTP/1.0" 이라 부르는 불완전한 응용 프로그램의 확산은, 프로토콜 버전의 변경을 필요로 하게 되었습니다. 
> (응용 프로그램들간의 신뢰가능한 통신을 위해) <br>
> 
> 이 스펙은 HTTP 1.1 프로토콜에 대해 정의합니다.<br>
> 이 프로토콜은 더욱 안정적인 구현을 보장하기 위해, HTTP 1.0 보다 더욱 엄격한 요구 사항이 포함되어 있습니다. <br>
> 
> 실제 정보 시스템은 단순 검색이외에도 동적 검색, 화면 업데이트, 주석과 같은 다양한 기능을 요구합니다. <br>
> HTTP 는 요청 목적을 나타내는 메서드들과 헤더들을 사용합니다. <br>
> 메서드가 적용될 리소스는 URI(URL, URN)를 기반으로 결정됩니다. <br>
> 메시지는 `Multipurose Internet Mail Extensions (MIME)` 에서 사용하는 것과 유사한 형식으로 전달됩니다. <br>
> 
> HTTP는 사용자 에이전트와 프록시/게이트웨이 간의 인터넷 통신을 위한 일반 프로토콜로도 사용됩니다. (SMTP, NNTP, FTP, Gopher, WAIS 프로토콜의 지원을 받으면서)<br>
> 이러한 방식으로 HTTP는 다양한 응용 프로그램에서 사용할 수 있는 리소스에 접근할 수 있는 basic hypermedia를 허용합니다. 

<br>

### 1.2 Requirements
**요구 사항**

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT",
"SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this
document are to be interpreted as described in RFC 2119. <br>

An implementation is not compliant if it fails to satisfy one or more
of the MUST or REQUIRED level requirements for the protocols it
implements. <br>
An implementation that satisfies all the MUST or REQUIRED
level and all the SHOULD level requirements for its protocols is said
to be "unconditionally compliant"; <br>
one that satisfies all the MUST
level requirements but not all the SHOULD level requirements for its
protocols is said to be "conditionally compliant." <br>

> 다음의 키워드들은 RFC 2119에서와 동일한 의미로 사용됩니다. <br>
> 
> MUST 혹은 REQUIRED 수준의 요구사항을 하나라도 만족하지 못하는 경우, 프로토콜 구현은 규정을 만족하지 않습니다. <br>
> MUST, REQUIRED, SHOULD 수준의 요구사항을 모두 만족하는 경우, 프로토콜 구현은 "무조건 준수" 상태입니다. <br>
> MUST 수준 요구사항은 모두 만족하지만, SHOULD 수준 요구사항은 모두 만족하지 못하는 경우, 프로토콜 구현은 "조건부 준수" 상태입니다.

<br>

### 1.3 Terminology
**술어**

This specification uses a number of terms to refer to the roles
played by participants in, and objects of, the HTTP communication. <br>

**connection** <br>
A transport layer virtual circuit established between two programs
for the purpose of communication. <br>

**message** <br>
The basic unit of HTTP communication, consisting of a structured
sequence of octets matching the syntax defined in section 4 and
transmitted via the connection. <br>

**request** <br>
An HTTP request message, as defined in section 5. <br> 

**response** <br>
An HTTP response message, as defined in section 6. <br>

**resource**<br>
A network data object or service that can be identified by a URI,
as defined in section 3.2. <br>
Resources may be available in multiple
representations (e.g. multiple languages, data formats, size, and
resolutions) or vary in other ways. <br>

**entity**<br>
The information transferred as the payload of a request or
response.<br>
An entity consists of metainformation in the form of
entity-header fields and content in the form of an entity-body, as
described in section 7.<br>

**representation**<br>
An entity included with a response that is subject to content
negotiation, as described in section 12. <br>
There may exist multiple
representations associated with a particular response status.<br>

**content negotiation**<br>
The mechanism for selecting the appropriate representation when
servicing a request, as described in section 12.<br>
The representation of entities in any response can be negotiated
(including error responses). <br>

**variant**<br>
A resource may have one, or more than one, representation(s)
associated with it at any given instant. <br>
Each of these
representations is termed a `variant`. <br>
Use of the term `variant`
does not necessarily imply that the resource is subject to content
negotiation. <br>

**client**<br>
A program that establishes connections for the purpose of sending
requests. <br>

**user agent**<br>
The client which initiates a request. <br>
These are often browsers,
editors, spiders (web-traversing robots), or other end user tools. <br>

**server**<br>
An application program that accepts connections in order to
service requests by sending back responses. <br>
Any given program may
be capable of being both a client and a server; <br>
our use of these
terms refers only to the role being performed by the program for a
particular connection, rather than to the program's capabilities
in general. <br>
Likewise, any server may act as an origin server, proxy, gateway, or tunnel, switching behavior based on the nature of each request.<br>

**origin server**<br>
The server on which a given resource resides or is to be created. <br>

**proxy**<br>
An intermediary program which acts as both a server and a client
for the purpose of making requests on behalf of other clients. <br>
Requests are serviced internally or by passing them on, with
possible translation, to other servers. <br>
A proxy MUST implement
both the client and server requirements of this specification. <br>
A "transparent proxy" is a proxy that does not modify the request or
response beyond what is required for proxy authentication and
identification. <br>
A "non-transparent proxy" is a proxy that modifies
the request or response in order to provide some added service to
the user agent, such as group annotation services, media type
transformation, protocol reduction, or anonymity filtering. <br>
Except where either transparent or non-transparent behavior is explicitly
stated, the HTTP proxy requirements apply to both types of proxies. <br>

**gateway**<br>
A server which acts as an intermediary for some other server. <br>
Unlike a proxy, a gateway receives requests as if it were the
origin server for the requested resource; <br>
the requesting client may not be aware that it is communicating with a gateway. <br>

**tunnel**<br>
An intermediary program which is acting as a blind relay between
two connections. <br>
Once active, a tunnel is not considered a party
to the HTTP communication, though the tunnel may have been
initiated by an HTTP request. <br>
The tunnel ceases to exist when both
ends of the relayed connections are closed. <br>

**cache**<br>
A program's local store of response messages and the subsystem
that controls its message storage, retrieval, and deletion. <br>
A cache stores cacheable responses in order to reduce the response
time and network bandwidth consumption on future, equivalent
requests. <br>
Any client or server may include a cache, though a cache
cannot be used by a server that is acting as a tunnel. <br>

**cacheable** <br>
A response is cacheable if a cache is allowed to store a copy of
the response message for use in answering subsequent requests. <br>
The rules for determining the cacheability of HTTP responses are
defined in section 13. <br>
Even if a resource is cacheable, there may be additional constraints on whether a cache can use the cached
copy for a particular request. <br>

**first-hand**<br>
A response is first-hand if it comes directly and without
unnecessary delay from the origin server, perhaps via one or more
proxies. <br>
A response is also first-hand if its validity has just
been checked directly with the origin server. <br>

**explicit expiration time** <br>
The time at which the origin server intends that an entity should
no longer be returned by a cache without further validation. <br>

**heuristic expiration time**<br>
An expiration time assigned by a cache when no explicit expiration
time is available. <br>

**age**<br>
The age of a response is the time since it was sent by, or
successfully validated with, the origin server. <br>

**freshness lifetime**<br>
The length of time between the generation of a response and its
expiration time. <br>

**fresh**<br>
A response is fresh if its age has not yet exceeded its freshness
lifetime. <br>

**stale**<br>
A response is stale if its age has passed its freshness lifetime. <br>

**semantically transparent**<br>
A cache behaves in a "semantically transparent" manner, with
respect to a particular response, when its use affects neither the
requesting client nor the origin server, except to improve
performance. <br>
When a cache is semantically transparent, the client
receives exactly the same response (except for hop-by-hop headers)
that it would have received had its request been handled directly
by the origin server. <br>

**validator**<br>
A protocol element (e.g., an entity tag or a Last-Modified time)
that is used to find out whether a cache entry is an equivalent
copy of an entity. <br>

**upstream/downstream**<br>
Upstream and downstream describe the flow of a message: all
messages flow from upstream to downstream. <br>

**inbound/outbound**<br>
Inbound and outbound refer to the request and response paths for
messages: "inbound" means "traveling toward the origin server",
and "outbound" means "traveling toward the user agent" <br>


> 이 스펙은 참여자들의 역할과 HTTP 통신의 대상들을 지칭하기 위한 여러 용어를 사용합니다. <br>
> 
> `connection` <br>
> 두 프로그램이 통신하기 위해 설치된 전송 계층의 가상 회선 <br>
> 
> `message` <br>
> HTTP 통신의 기본 단위로, 구조화된 바이트 문자열로 이루어져 있으며 섹션 4에 정의된 구문과 일치하고, 커넥션을 통해 전송됩니다. <br>
> 
> `request` <br>
> HTTP 요청 메시지로, 섹션 5에서 다룹니다. <br>
> 
> `response` <br>
> HTTP 응답 메시지로, 섹션 6에서 다룹니다. <br>
> 
> `resource` <br>
> URI 로 식별되는 네트워크 데이터 또는 서비스입니다. (섹션 3.2에서 다룹니다.)<br>
> 리소스는 여러 언어, 데이터 형식, 크기, 해상도와 같은 여러 표현으로 제공됩니다. <br>
> 
> `entity`<br>
> 요청 또는 응답의 payload로 전송되는 정보입니다.<br>
> `entity` 는 `entity-header` 필드의 메타정보와 `entity-body` 의 내용으로 구성됩니다. (섹션 7에서 다룹니다.)<br>
>
> `representation` <br>
> content 협상의 대상이 되는 응답을 포함한 엔티티입니다.<br>
> 특정 응답 상태와 관련있는 여러 표현이 존재할 수 있습니다. <br>
> 
> `content negotiation`<br>
> 요청을 처리할 때, 적절한 표현을 선택하는 메커니즘 입니다.<br>
> 모든 응답에서, 엔티티의 표현은 협상의 대상이 됩니다. (오류 응답 포함) <br>
> 
> `variant` (변형) <br>
> 자원은 주어진 순간에, 하나 이상의 표현을 가질 수 있습니다. <br>
> 이러한 표현들을 '변형'이라 합니다. <br>
> '변형'이라는 용어를 사용한다 해서, 반드시 리소스가 content 협상 대상임을 의미하진 않습니다. <br>
> 
> `client` <br>
> 요청을 보낼 목적으로 connection 을 수립하는 프로그램입니다. <br>
> 
> `user agent` <br>
> 요청을 시작하는 클라이언트 <br>
> 이들은 브라우저, 에디터, spiders (web-traversing robots) 또는 end-user tool 입니다.<br>
> 
> `server` <br>
> 요청에 대한 응답을 보내기 위해, connection 을 수락하는 응용 프로그램입니다.<br>
> 모든 프로그램은 클라이언트와 서버가 모두 될 수 있습니다.<br>
> 이러한 용어의 사용은 프로그램이 일반적으로 수행할 수 있는 능력을 말하는 것이 아니라, 특정 연결에 대해 프로그램이 수행하는 역할만을 얘기합니다.<br>
> 마찬가지로, 모든 서버는 각 요청의 특성에 따라 origin server, proxy, gateway, tunnel 역할을 할 수 있습니다 .<br>
>
> `origin server` <br>
> 리소스가 상주하거나 생성되는 서버입니다.<br>
> 
> `proxy` <br>
> 다른 클라이언트를 대신하여 서버나 클라이언트에게 요청을 하는 중개 프로그램입니다.<br>
> 요청을 내부적으로 처리하거나 또는 가능한 변환을 한 채 다른 서버에 전달할 수 있습니다. <br>
> 프록시는 서버와 클라이언트의 요구사항을 모두 구현해야 합니다.<br>
> "투명 프록시" 는 프록시 인증 및 식별에 필요한 것 이상으로는 요청이나 응답을 수정하지 않는 프록시입니다.<br>
> "불투명 프록시" 는 `user agent` 에 그룹 주석 서비스, 미디어 유형 변환, 프로토콜 축소, 익명성 필터링과 같은
> 추가적인 서비스를 제공하기 위해 요청이나 응답을 수정하는 프록시입니다. <br>
> 투명 혹은 불투명한 동작이 명시적으로 명시된 경우가 아니라면, HTTP 프록시 요구 사항은 두 프록시 유형에 모두 적용됩니다. <br>
>
> `gateway`<br>
> 다른 서버의 중개자 역할을 하는 서버입니다. <br>
> 프록시와 다른 점은, 게이트웨이는 원본 서버인 것처럼 리소스 요청을 받습니다.<br>
> 요청 클라이언트는 게이트웨이와 통신하고 있음을 알지 못합니다. <br>
>
> `tunnel`<br>
> 두 연결 사이에서 `blind relay` 를 수행하는 중개 프로그램입니다. <br>
> 일단 활성화되면, HTTP 요청에 의해 터널이 시작되었더라도, 터널을 HTTP 통신의 당사자로 간주하지 않습니다. <br>
> 릴레이된 연결의 양쪽 끝이 닫히면, 터널은 사라집니다. <br>
> 
> `cache` <br>
> 응답 메시지의 로컬 저장소 역할과, 메시지 저장소, 검색 및 삭제를 제어하는 하위 시스템입니다. <br>
> 캐시는 향후 동일한 요청에 대해 응답시간 및 네트워크 대역폭 소비를 줄이기 위해, 캐시 가능한 응답들을 저장합니다. <br>
> 모든 클라이언트나 서버는 캐시를 포함할 수 있지만, 터널 역할을 하는 서버에서는 캐시를 사용할 수 없습니다. <br>
>
> `cacheable` (캐시가능)<br>
>  후속 요청에 응답하기 위한 응답 메시지 복사본을 저장할 수 있도록 캐시가 허락한 경우, 응답은 캐시 가능합니다. <br>
> HTTP 응답의 캐시 가능성을 결정하는 규칙은 섹션 13에 정의되어 있습니다. <br>
> 리소스가 캐시가능하더라도, 특정 요청에 대해 캐시된 복사본을 사용할 수 있는지에 대한 추가적인 제약 조건이 있을 수 있습니다. <br>
> 
> `first-hand` (직접) <br>
> 프록시를 거치는 등의 불필요한 지연없이 origin server 에서 직접 응답이 오는 경우, 이 응답을 직접 응답이라 합니다. <br>
> origin server 가 직접 유효성을 체크한 응답도 직접 응답이라 합니다. <br>
>
> `explicit expiration time` (명시적 만료 시간)<br>
> origin server 가 추가적인 유효성 검사 없이 캐시에서 엔티티를 더 이상 꺼내쓸 수 없게 의도하는 시간입니다. <br>
>
> `heuristic expiration time` (모호한 만료 시간)<br>
> 명시적인 만료시간을 사용할 수 없을 때, 캐시에서 할당하는 만료시간 입니다. <br>
> 
> `age` <br>
> 응답의 나이는 `origin server` 가 응답 메시지를 성공적으로 보낸 이후부터의 시간입니다.<br> 
> 
> `freshness lifetime`<br>
> 응답 메시지가 생성된 시간과 응답 메시지의 만료 시간 사이의 시간입니다. <br>
> 
> `fresh`<br>
> 응답 메시지의 나이가 아직 `freshness lifetime` 을 초과하지 않았다면 응답 메시지는 `fresh` 합니다. <br>
> 
> `stale`(신선하지 않은) <br>
> 응답 메시지의 나이가 `freshness lifetime` 을 초과하였다면 응답 메시지는 `stale` 합니다. <br>
> 
> `semantically transparent` (의미상 투명한) <br> 
> (특정 응답 에서) 캐시는 성능 향상을 제외하고는, 클라이언트와 서버에 영향을 미치지 않으므로 "의미상 투명한" 방식으로 작동합니다. <br>
> 캐시가 `semantically transparent` 하다면, 클라이언트는 원본 서버로부터 받았어야 할 응답 메시지와 동일한 응답을 받습니다. <br>
> 
> `validator` <br>
> (`entity tag` 또는 `Last-Modified time` 같은) 프로토콜 요소를 말하며, 캐시 항목이 로컬의 복사본과 동일한 지 검증합니다. <br>
>
> `upstream/downstream` <br>
> `upstream` 과 `downstream` 은 메시지의 흐름을 설명합니다. <br>
> 모든 메시지들은 `upstream` 에서 `downstream` 으로 흐릅니다. <br>
> 
> `inbound/outbound`<br>
> `inbound` 와 `outbound` 는 요청 메시지와 응답 메시지의 이동 경로를 나타냅니다. <br>
> `inbound` 는 `origin server` 로 향하는 이동을 의미하고 `outbound` 는 `user agent` 를 향하는 이동을 의미합니다. <br>

### 1.4 Overall Operation
**전반적인 운영**

The HTTP protocol is a request/response protocol.
A client sends a request to the server in the form of a request method, URI, and protocol version, followed by a MIME-like message containing request modifiers, client information, and possible body content over a connection with a server.
The server responds with a status line, including the message's protocol version and a success or error code, followed by a MIME-like message containing server information, entity metainformation, and possible entity-body content.
The relationship between HTTP and MIME is described in appendix 19.4.
<br>

Most HTTP communication is initiated by a user agent and consists of a request to be applied to a resource on some origin server.
In the simplest case, this may be accomplished via a single connection (v) between the user agent (UA) and the origin server (O).
```
request chain ------------------------>
UA -------------------v------------------- O
<----------------------- response chain
```
A more complicated situation occurs when one or more intermediaries are present in the request/response chain.
There are three common forms of intermediary: proxy, gateway, and tunnel.
A proxy is a forwarding agent, receiving requests for a URI in its absolute form, rewriting all or part of the message, and forwarding the reformatted request toward the server identified by the URI.
A gateway is a receiving agent, acting as a layer above some other server(s) and, if necessary, translating the requests to the underlying server's protocol.
A tunnel acts as a relay point between two connections without changing the messages;
tunnels are used when the
communication needs to pass through an intermediary (such as a firewall) even when the intermediary cannot understand the contents of the messages.


> HTTP 프로토콜은 요청/응답 프로토콜입니다.
> 클라이언트는 request method, URI, 프로토콜 버전, 요청 수정자를 포함한 MIME 유형의 메시지, 클라이언트 정보, 가능한 본문 메시지를 서버에게 전송합니다.
> 서버는 메시지의 프로토콜 버전과 상태코드, 서버 정보를 포함한 MIME 유형의 메시지, 엔티티 메타 정보와 가능한 엔티티 본문 정보를 가진 status line 을 반환합니다.
> HTTP 와 MIME의 관계는 부록 19.4에서 설명합니다.
> <br>
> 
> 대부분의 HTTP 통신은 사용자 에이전트가 시작하며, 일부 origin server 의 리소스에 적용되는 요청으로 이루어집니다. 
> 가장 간단한 경우, 사용자 에이전트와 origin server의 단일 커넥션을 통해 수행됩니다.
> ```
> request chain ------------------------>
> UA -------------------v------------------- O
> <----------------------- response chain
> ```
> 요청/응답 체인 중간에 하나 이상의 중개자가 있는 경우, 더 복잡한 상황이 발생합니다.
> 중개자에는 프록시, 게이트웨이, 터널의 3가지 형태가 있습니다.
> 프록시는 전달 에이전트로, 요청으로부터 절대 URI를 받고, 메시지를 다시 재작성하고, URI로 식별되는 서버에 새로이 포맷된 요청 메시지를 전달합니다.
> 게이트웨이는 다른 서버위의 계층 역할을 하는 수신자 에이전트로, 필요한 경우 요청메시지의 프로토콜을 해당 서버에 맞게 변환합니다.
> 터널은 메시지를 변경하지 않고, 두 connection 을 연결합니다. 
> 중개자가 이해할 수 없는 메시지라 하더라도, 통신이 제대로 이루어지기 위해선 중개자를 통과해야 합니다.

<br>

```
request chain -------------------------------------->
UA -----v----- A -----v----- B -----v----- C -----v----- O
<------------------------------------- response chain
```
The figure above shows three intermediaries (A, B, and C) between the user agent and origin server.
A request or response message that travels the whole chain will pass through four separate connections.
This distinction is important because some HTTP communication options may apply only to the connection with the nearest, non-tunnel neighbor, only to the end-points of the chain, or to all connections along the chain.
Although the diagram is linear, each participant may be engaged in multiple, simultaneous communications.
For example, B may be receiving requests from many clients other than A, and/or forwarding requests to servers other than C, at the same time that it is handling A's request.

> ```
> request chain -------------------------------------->
> UA -----v----- A -----v----- B -----v----- C -----v----- O
> <------------------------------------- response chain
>```
> 위 그림은 사용자 에이전트와 origin server 사이 중개자 A, B, C 를 보여줍니다.
> 요청 및 응답 메시지는 전체 통신과정에서 4개의 개별적인 연결을 통과합니다.
> 일부 HTTP 통신 옵션은 연결된 커넥션에서만 적용되거나, 터널이 아닌 이웃에 적용되거나, end-point에서만 적용되거나, 모든 연결에 적용되는 등, 차이가 있기 때문에 이러한 개별적인 연결의 구분은 중요합니다.
> 그림은 선형적이지만, 실제 각 연결에서 참여자는 여러 통신에 동시적으로 참여할 수 있습니다.
> 예를 들어, B는 A보다 더 많은 요청을 받거나, A의 요청을 처리하는 동시에 C가 아닌 서버에 요청을 전달할 수 있습니다.

<br>

Any party to the communication which is not acting as a tunnel may employ an internal cache for handling requests.
The effect of a cache is that the request/response chain is shortened if one of the participants along the chain has a cached response applicable to that request.
The following illustrates the resulting chain if B has a cached copy of an earlier response from O (via C) for a request which has not been cached by UA or A.
```
request chain ---------->
UA -----v----- A -----v----- B - - - - - - C - - - - - - O
<--------- response chain
```
Not all responses are usefully cacheable, and some requests may contain modifiers which place special requirements on cache behavior.
HTTP requirements for cache behavior and cacheable responses are defined in section 13.

> 터널 역할을 하지 않는 통신 당사자는, 내부 캐시를 사용하여 요청을 처리할 수 있습니다.
> 통신 당사자들 중 하나라도 요청에 적용가능한 캐시된 응답을 갖고 있으면, 요청/응답 체인을 줄일 수 있습니다.
> 다음은 B가 O로 부터 받은 이전 응답의 캐시된 사본을 갖는 경우에 응답 체인을 보여줍니다.
>```
>request chain ---------->
>UA -----v----- A -----v----- B - - - - - - C - - - - - - O
><--------- response chain
>```
> 모든 응답이 캐시 가능한 것은 아니며, 특정 요청은 특별한 캐시 동작을 요청하는 수정자를 포함할 수 있습니다.
> 캐시 동작과 캐시 가능한 응답에 관한 HTTP 요구사항은 13장에 정의되어 있습니다.

<br>

In fact, there are a wide variety of architectures and configurations of caches and proxies currently being experimented with or deployed across the World Wide Web.
These systems include national hierarchies of proxy caches to save transoceanic bandwidth, systems that broadcast or multicast cache entries, organizations that distribute subsets of cached data via CD-ROM, and so on.
HTTP systems are used in corporate intranets over high-bandwidth links, and for access via PDAs with low-power radio links and intermittent connectivity.
The goal of HTTP/1.1 is to support the wide diversity of configurations already deployed while introducing protocol constructs that meet the needs of those who build web applications that require high reliability and, failing that, at least reliable indications of failure.


> 실제로, `World Wide Web` 에는 실험중이거나 사용중인 캐시와 프록시의 다양한 구조와 설정이 있습니다.
> 이러한 시스템에는 대양 횡단 대역폭을 절약하기 위한 국가 계층의 프록시, `broadcast` 나 `multicast` 하기 위한 캐시들, CD-ROM을 통해 캐시의 하위 집합을 배포하는 조직등이 포함됩니다.
> HTTP 시스템은 고대역폭 링크를 통해 회사 인트라넷에서 사용되며 저전력의 무선 링크나 간헐적인 연결이 있는 PDA를 통한 접근에 사용됩니다.
> HTTP/1.1 의 목표는 이미 배포된 다양한 구성을 지원하고 웹 애플리케이션을 만드는 사람들의 요구사항(높은 신뢰성, 실패할 경우 최소한 신뢰할 수 있는 고장 표시)을 만족하는 프로토콜을 만드는 것입니다. 

<br>

HTTP communication usually takes place over TCP/IP connections.
The default port is TCP 80, but other ports can be used.
This does not preclude HTTP from being implemented on top of any other protocol on the Internet, or on other networks.
HTTP only presumes a reliable transport; any protocol that provides such guarantees can be used;
the mapping of the HTTP/1.1 request and response structures onto the transport data units of the protocol in question is outside the scope of this specification.
In HTTP/1.0, most implementations used a new connection for each request/response exchange.
In HTTP/1.1, a connection may be used for one or more request/response exchanges, although connections may be closed for a variety of reasons

> HTTP 통신은 일반적으로 TCP/IP 연결을 통해 이루어집니다.
> 기본 포트는 80번 이지만, 다른 포트로 사용 가능합니다.
> 이는 HTTP가 다른 프로토콜 위에 구현되는 것이 가능함을 의미합니다.
> HTTP 는 신뢰할 수 있는 전송만을 가정합니다; 이러한 보증을 제공하는 모든 프로토콜을 사용할 수 있습니다.
> `Transport` 계층의 프로토콜과 데이터 단위에 대한 논의는 이 스펙의 범위를 벗어납니다. 
> HTTP/1.0 에서 대부분은 매 요청/응답 마다 새로운 커넥션을 사용했습니다.
> HTTP/1.1 에서는 하나의 커넥션이 한개 이상의 요청/응답 트랜잭션을 처리할 수 있습니다. (비록 트랜잭션 처리도중에 다양한 이유로 커넥션이 닫힐 수 있지만)

## 2. Notational Conventions and Generic Grammar
> 표기 규칙 및 일반 문법 

### 2.1 Augmented BNF

> 2.1 강화된 BNF

<br>

All of the mechanisms specified in this document are described in both prose and an augmented Backus-Naur Form (BNF) similar to that used by RFC 822 [9].
Implementors will need to be familiar with the notation in order to understand this specification.
The augmented BNF includes the following constructs:

name = definition
The name of a rule is simply the name itself (without any enclosing "<" and ">") and is separated from its definition by the equal "=" character.
White space is only significant in that indentation of continuation lines is used to indicate a rule definition that spans more one line.
Certain basic rules are in uppercase, such as SP, LWS, HT, CRLF, DIGIT, ALPHA, etc.
Angle brackets are used within definitions whenever their presence will facilitate discerning the use of rule names.

"literal"
Quotation marks surround literal text.
Unless stated otherwise, the text is case-insensitive.

rule1 | rule2
Elements separated by a bar ("|") are alternatives, e.g., "yes | no" will accept yes or no.

(rule1 rule2)
Elements enclosed in parentheses are treated as a single element.
Thus, "(elem (foo | bar) elem)" allows the token sequences "elem foo elem" and "elem bar elem".

*rule
The character "*" preceding an element indicates repetition.
The full form is "<n>*<m>element" indicating at least <n> and at most <m> occurrences of element.
Default values are 0 and infinity so that "*(element)" allows any number, including zero;
"1*element" requires at least one; and "1*2element" allows one or two.

[rule]
Square brackets enclose optional elements; "[foo bar]" is equivalent to "*1(foo bar)".

N rule
Specific repetition: "<n>(element)" is equivalent to "<n>*<n>(element)"; that is, exactly <n> occurrences of (element).
Thus 2DIGIT is a 2-digit number, and 3ALPHA is a string of three alphabetic characters.

#rule
A construct "#" is defined, similar to "*", for defining lists of elements.
The full form is "<n>#<m>element" indicating at least <n> and at most <m> elements, each separated by one or more commas (",") and OPTIONAL linear white space (LWS).
This makes the usual form of lists very easy; a rule such as ( *LWS element *( *LWS "," *LWS element )) can be shown as 1#element
Wherever this construct is used, null elements are allowed, but do not contribute to the count of elements present.
That is, "(element), , (element) " is permitted, but counts as only two elements.
Therefore, where at least one element is required, at least one non-null element MUST be present.
Default values are 0 and infinity so that "#element" allows any number, including zero; "1#element" requires at least one; and "1#2element" allows one or two.

; comment
A semi-colon, set off some distance to the right of rule text, starts a comment that continues to the end of line.
This is a simple way of including useful notes in parallel with the specifications.

> 이 문서에 명시된 모든 메커니즘은 산문과 RFC 822에서 사용하는 것과 유사한 증강된 BNF 로 설명되어 있습니다.
> 구현자는 이 스펙을 잘 이해하기 위해선, 표기법을 잘 알고 있어야 합니다.
> 증강된 BNF 는 다음의 구조로 이루어집니다. 
> 
> `name = definiton`
> 규칙의 이름은 이름 그 자체이며 (< 나 > 없이 표현) 정의와 동일한 '=' 문자로 구분됩니다.
> 공백은 연속되는 줄의 들여쓰기가 한 줄 이상에 걸쳐있는 규칙 정의를 나타내는 데 사용된다는 점에서만 중요합니다.
> SP, LWS, HT, CRLF, DIGIT, ALPHA 같은 특정 기본 규칙은 대문자로 되어 있습니다.
> 대괄호는 정의 내에서 규칙 이름을 쉽게 구분할 수 있는 경우에만 사용됩니다.
> 
> `"literal"`
> 따옴표는 문자를 둘러싸고 있습니다.
> 달리 명시되지 않는 한, 텍스트는 대소문자를 구분하지 않습니다.
> 
> `rule1 | rule2`
> `|` 로 구분된 요소들은 서로 대체가능한 항목입니다. 예를들어 `"yes | no"` 는 yes 나 no 를 허용합니다.
> 
> `(rule1 rule2)`
> 괄호로 묶인 요소는 단일 요소로 취급됩니다.
> 따라서 `"(elem (foo | bar) elem)"` 은 `"elem foo elem"` 또는 `"elem bar elem"` 순서의 토큰을 허용합니다.
> 
> `*rule`
> 요소 앞의 `*` 문자는 반복을 나타냅니다.
> 전체형식은 `"<n>*<m>"`로, 최소 `n` 개에서 최대 `m`개로 이루어져 있는 요소임을 나타냅니다.
> 기본값은 0과 무한대이므로, `"*(요소)` 는 0을 포함한 모든 숫자를 허용합니다.
> `1*element` 는 최소 한개가 필요하고, `1*2element` 는 1~2개를 허용합니다.
> 
> `[rule]`
> 대괄호는 선택적인 요소를 묶습니다. `[foo bar]` 는 `*1(foo bar)` 와 동일합니다.
> 
> `N rule`
> 특정 반복: `<n>(element)` 는 `<n>*<n>(element)` 와 동일합니다; 즉, `n` 번 요소가 반복되어야 합니다.
> 따라서 2DIGIT 은 두자리 숫자이고, 3ALPHA 는 세개의 알파벳 문자로 이루어진 문자열입니다.
> 
> `#rule`
> `#` 은 `*` 과 유사하게 요소 목록들을 정의합니다.
> 전체 형식은 `<n>#<m>element` 로 최소 n개에서 최대 m개의 요소를 나타내며, 각각은 하나이상의 쉼표와 선택적 선형 공백으로 구분합니다.
> 이렇게 하면 일반적인 형태의 목록을 쉽게 만들 수 있습니다; `( *LWS element *( *LWS "," *LWS element ))` 와 같은 규칙은 `1#element` 로 표시할 수 있습니다.
> 이 구조체가 사용되면 널값도 허용하지만, 이는 요소 개수를 증가시키진 않습니다.
> 즉 `(element), , (element) ` 는 허용되나, 오직 2개의 요소로만 계산됩니다.
> 그래서, 적어도 하나의 요소가 필요한 경우, null 이 아닌 요소가 하나 이상 반드시 존재해야 합니다.
> 기본값은 0 ~ 무한대이므로 `#element` 는 0을 포함한 어떤 숫자든 허용합니다; `1#element` 는 최소 한개의 요소를 허용하고; `1#2element` 는 한개 혹은 두개의 요소를 허용합니다.
> 
> ` ; comment`
> 규칙 텍스트 오른쪽에 일정 거리를 두고 세미콜론을 사용하면, 줄 끝에서 이어지는 댓글을 시작합니다. 
> 이것은 스펙과 함께 유용한 메모를 포함하는 간단한 방법입니다. 