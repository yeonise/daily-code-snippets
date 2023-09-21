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

implied *LWS
The grammar described by this specification is word-based.
Except where noted otherwise, linear white space (LWS) can be included between any two adjacent words (token or quoted-string), and between adjacent words and separators, without changing the interpretation of a field.
At least one delimiter (LWS and/or separators) MUST exist between any two tokens (for the definition of "token" below), since they would otherwise be interpreted as a single token.


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
> 
> `*LWS` 암시
> 이 스펙에 묘사된 문법은 단어 기반입니다.
> 특별히 명시된 경우를 제외하고, 인접한 두 단어 (토큰이나 따옴표로 묶인 문자열) 사이나, 인접한 단어와 구분 기호 사이에는 필드의 해석을 변경하지 않고, 선형 공백(LSW)을 포함할 수 있습니다. 
> 최소한 하나의 구분자 (LWS 나/또는 다른 구분자들) 가 두개의 토큰 사이에 반드시 존재해야 합니다, 왜냐하면 그들은 단일 토큰으로 해석될 수 있기 때문에.

<br>

### 2.2 Basic Rules

> 기본 규칙

<br>

The following rules are used throughout this specification to describe basic parsing constructs. 
The US-ASCII coded character set is defined by ANSI X3.4-1986 [21].

```
 OCTET          = <any 8-bit sequence of data>
       CHAR           = <any US-ASCII character (octets 0 - 127)>
       UPALPHA        = <any US-ASCII uppercase letter "A".."Z">
       LOALPHA        = <any US-ASCII lowercase letter "a".."z">
       ALPHA          = UPALPHA | LOALPHA
       DIGIT          = <any US-ASCII digit "0".."9">
       CTL            = <any US-ASCII control character
                        (octets 0 - 31) and DEL (127)>
       CR             = <US-ASCII CR, carriage return (13)>
       LF             = <US-ASCII LF, linefeed (10)>
       SP             = <US-ASCII SP, space (32)>
       HT             = <US-ASCII HT, horizontal-tab (9)>
       <">            = <US-ASCII double-quote mark (34)>
```

HTTP/1.1 defines the sequence CR LF as the end-of-line marker for all protocol elements except the entity-body (see appendix 19.3 for tolerant applications).
The end-of-line marker within an entity-body is defined by its associated media type, as described in section 3.7.

```
CRLF           = CR LF
```

HTTP/1.1 header field values can be folded onto multiple lines if the continuation line begins with a space or horizontal tab.
All linear white space, including folding, has the same semantics as SP.
A recipient MAY replace any linear white space with a single SP before interpreting the field value or forwarding the message downstream.

```
LWS            = [CRLF] 1*( SP | HT )
```

The TEXT rule is only used for descriptive field contents and values that are not intended to be interpreted by the message parser.
Words of *TEXT MAY contain characters from character sets other than ISO-8859-1 [22] only when encoded according to the rules of RFC 2047 [14].

```
       TEXT           = <any OCTET except CTLs,
                        but including LWS>
```

A CRLF is allowed in the definition of TEXT only as part of a header field continuation.
It is expected that the folding LWS will be replaced with a single SP before interpretation of the TEXT value.

Hexadecimal numeric characters are used in several protocol elements.
```
       HEX            = "A" | "B" | "C" | "D" | "E" | "F"
                      | "a" | "b" | "c" | "d" | "e" | "f" | DIGIT
```

Many HTTP/1.1 header field values consist of words separated by LWS or special characters.
These special characters MUST be in a quoted string to be used within a parameter value (as defined in section 3.6).

```
       token          = 1*<any CHAR except CTLs or separators>
       separators     = "(" | ")" | "<" | ">" | "@"
                      | "," | ";" | ":" | "\" | <">
                      | "/" | "[" | "]" | "?" | "="
                      | "{" | "}" | SP | HT
```

Comments can be included in some HTTP header fields by surrounding the comment text with parentheses.
Comments are only allowed in fields containing "comment" as part of their field value definition.
In all other fields, parentheses are considered part of the field value.

```
        comment        = "(" *( ctext | quoted-pair | comment ) ")"
        ctext          = <any TEXT excluding "(" and ")">
```

A string of text is parsed as a single word if it is quoted using double-quote marks.

```
       quoted-string  = ( <"> *(qdtext | quoted-pair ) <"> )
       qdtext         = <any TEXT except <">>
```

The backslash character ("\") MAY be used as a single-character quoting mechanism only within quoted-string and comment constructs.

```
        quoted-pair    = "\" CHAR
```

> 이 스펙에서 기본 파싱 구조를 설명하기 위해, 다음의 규칙들이 사용됩니다.
> US-ASCII 문자 집합은 ANSI X3.4-1986 에 정의되어 있습니다.
> 
> (코드 생략)
> 
> HTTP/1.1 은 entity-body 를 제외한 모든 프로토콜 요소의 end-of-line 마커를 CR LF 시퀸스로 정의합니다. (허용가능한 애플리케이션에 대해서는 부록 19.3 을 참조하십시오.)
> entity-body 의 end-of-line 마커는 섹션 3.7에 설명된대로 연관된 media type 에 의해 정의됩니다.
> 
> (코드 생략)
> 
> 만약 연속된 줄이 공백이나 가로 탭으로 시작된다면, HTTP/1.1 헤더 필드 값을 여러 줄로 접을 수 있습니다.
> 접기를 포함한 모든 선형 공백은 SP와 동일한 의미를 갖습니다.
> 수신자는 필드 값을 해석하거나 메시지를 다운스트림으로 전달하기 전에 모든 선형 공백을 단일 SP로 대체할 수 있습니다.
> 
> (코드 생략)
> 
> TEXT 규칙은 message 파서가 해석할 의도가 없는 설명적인 필드 콘텐츠나 값에만 사용됩니다.
> TEXT의 단어는 RFC 2047의 규칙에 따라 인코딩된 경우에만 ISO-8859-1 이외의 문자 집합의 문자를 포함할 수 있습니다.
> 
> (코드 생략)
>
> CRLF는 연속된 헤더 필드의 일부로만 TEXT 정의에서 허용됩니다.
> 접혀있는 LWS 는 TEXT 값을 해석하기 전에 단일 SP로 대체될 것으로 예상됩니다.
> 
> 16진수 문자는 여러 프로토콜 요소로 사용됩니다.
>
> (코드 생략)
> 
> 많은 HTTP/1.1 헤더 값은 LWS나 특수 문자로 구분된 단어로 이루어져 있다.
> 특수 문자들은 파라미터 값 내에서 사용하려면 반드시 따옴표로 묶인 문자열 안에 있어야 합니다. (섹션 3.6에 정의되어 있습니다.)
> 
> (코드 생략)
> 
> 댓글 텍스트를 괄호로 묶어 일부 HTTP 헤더 필드에 댓글을 포함할 수 있습니다. 
> 댓글은 필드 값 정의의 일부로 "댓글"이 포함되어 있는 필드에서만 허용됩니다.
> 다른 모든 필드에서는, 괄호들은 필드 값의 일부로 간주됩니다.
> 
> (코드 생략)
> 
> 텍스트 문자열이 큰 따옴표로 묶여 있다면, 단일 단어로 파싱됩니다.
> 
> (코드 생략)
> 
> 따옴표 문자열이나 댓글 구조 내에서만 역슬래쉬를 단일 문자 인용 메커니즘으로 사용할 수 있습니다.
> 
> (코드 생략)

<br>

## 3. Protocol Parameters

> 프로토콜 매개변수

<br>

### 3.1 HTTP Version

> HTTP 버전

<br>

HTTP uses a "<major>.<minor>" numbering scheme to indicate versions of the protocol.
The protocol versioning policy is intended to allow the sender to indicate the format of a message and its capacity for understanding further HTTP communication, rather than the features obtained via that communication.
No change is made to the version number for the addition of message components which do not affect communication behavior or which only add to extensible field values.
The <minor> number is incremented when the changes made to the protocol add features which do not change the general message parsing algorithm, but which may add to the message semantics and imply additional capabilities of the sender.
The <major> number is incremented when the format of a message within the protocol is changed.
See RFC 2145 [36] for a fuller explanation.
The version of an HTTP message is indicated by an HTTP-Version field in the first line of the message.

```
        HTTP-Version   = "HTTP" "/" 1*DIGIT "." 1*DIGIT
```

Note that the major and minor numbers MUST be treated as separate integers and that each MAY be incremented higher than a single digit.
Thus, HTTP/2.4 is a lower version than HTTP/2.13, which in turn is lower than HTTP/12.3. 
Leading zeros MUST be ignored by recipients and MUST NOT be sent.

An application that sends a request or response message that includes HTTP-Version of "HTTP/1.1" MUST be at least conditionally compliant with this specification.
Applications that are at least conditionally compliant with this specification SHOULD use an HTTP-Version of "HTTP/1.1" in their messages, and MUST do so for any message that is not compatible with HTTP/1.0.
For more details on when to send specific HTTP-Version values, see RFC 2145 [36].

The HTTP version of an application is the highest HTTP version for which the application is at least conditionally compliant.

Proxy and gateway applications need to be careful when forwarding messages in protocol versions different from that of the application.
Since the protocol version indicates the protocol capability of the sender, a proxy/gateway MUST NOT send a message with a version indicator which is greater than its actual version.
If a higher version request is received, the proxy/gateway MUST either downgrade the request version, or respond with an error, or switch to tunnel behavior.

Due to interoperability problems with HTTP/1.0 proxies discovered since the publication of RFC 2068[33], caching proxies MUST, gateways MAY, and tunnels MUST NOT upgrade the request to the highest version they support.
The proxy/gateway's response to that request MUST be in the same major version as the request.

Note: Converting between versions of HTTP may involve modification of header fields required or forbidden by the versions involved.


> HTTP는 "<메이저>.<마이너>" 번호 체계를 사용하여 프로토콜의 버전을 표현합니다.
> 프로토콜 버전 관리 정책은 발신자가 해당 통신을 통해 얻은 기능보다는, 메시지의 형식과 추가 HTTP 통신을 이해할 수 있는 능력을 표시할 수 있도록 하기 위한 것입니다.
> 통신 동작에 영향을 주지 않거나, 확장 가능한 필드 값에만 추가되는 메시지 구성요소의 추가는 버전 번호를 변경시키지 않습니다.
> 프로토콜 변경으로 인해 일반적인 메시지 분석 알고리즘은 바뀌지 않지만, 메시지에 의미가 추가되고 발신자의 추가 기능을 암시할 수 있는 기능이 추가된다면 <minor> 번호가 증가합니다.
> 프로토콜 변경으로 인해 메시지를 주고받는 형태가 바뀌면 <메이저> 번호가 증가합니다.
> 더욱 자세한 정보는 RFC 2145를 참고하십시오. 
> HTTP 메시지의 버전은 메시지 첫 번째 줄의 HTTP-Version 필드에 표시됩니다.
> 
> (코드 생략)
> 
> 메이저와 마이너 번호는 분리된 정수로 관리되어야하고, 각각은 한자리 수보다 크게 증가할 수 있습니다.
> 그래서, HTTP/2.4 는 HTTP/2.13 보다 하위 버전이고, HTTP/12.3 보다도 하위 버전입니다.
> 0으로 시작되는 버전 번호는 수신자가 반드시 무시해야 하고, 절대로 보내선 안됩니다.
> 
> HTTP/1.1 버전을 포함하는 요청과 응답 메시지를 보내는 애플리케이션은 최소한 조건부로 이 스펙을 반드시 준수해야 합니다.
> 이 스펙을 조건부로 준수하는 애플리케이션은 메시지에 HTTP/1.1 버전임을 명시해야 하며, HTTP/1.0 과 호환되지 않는 메시지에는 반드시 HTTP/1.1 버전임을 명시해야 합니다. 
> 특정 HTTP 버전 값을 언제 전송해야 하는지 자세히 알고 싶다면, RFC 2145를 대한 스펙을 참고하십시오.
> 
> 애플리케이션의 HTTP 버전은 애플리케이션이 최소한의 조건을 만족하는 가장 높은 HTTP 버전입니다.
> 
> 프록시와 게이트웨이는 애플리케이션과 다른 프로토콜 버전으로 메시지를 포워딩할때 주의해야 합니다.
> 프로토콜 버전은 발신자의 프로토콜 기능을 나타내므로, 프록시/게이트웨이는 실제 버전보다 상위 버전의 메시지를 보내선 안됩니다.
> 만약 상위 버전의 요청을 받게되면, 프록시/게이트웨이는 요청 메시지의 프로토콜 버전을 낮추거나, 에러로 응답하거나, 터널 동작으로 전환해야 합니다.
> 
> RFC 2068에서 발견된 HTTP/1.0 프록시와의 상호 운용성 문제로 인해, 캐싱 프록시는 반드시 지원하는 최고 버전으로 업그레이드, 게이트 웨이는 가능하면 업그레이드, 터널은 절대로 요청을 업그레이드 해선 안됩니다. 
> 프록시/게이트웨이의 응답은 반드시 요청과 같은 메이저 버전을 가져야 합니다.
> 
> 참고: HTTP 버전 간 변환 시 관련 버전에서 요구하거나 금지하는 헤더 필드의 수정이 필요할 수 있습니다.

<br>

### 3.2 Uniform Resource Identifiers

> 단일 자원 식별자

<br>

URIs have been known by many names: WWW addresses, Universal Document Identifiers, Universal Resource Identifiers [3], and finally the combination of Uniform Resource Locators (URL) [4] and Names (URN) [20].
As far as HTTP is concerned, Uniform Resource Identifiers are simply formatted strings which identify--via name, location, or any other characteristic--a resource.

> URI는 다양한 이름으로 알려져 있습니다: WWW 주소, 범용 문서 식별자, 범용 리소스 식별자, 마지막으로 범용 리소스 위치와 이름의 조합
> HTTP에 관한 한, URI(범용 리소스 식별자) 는 이름, 위치 또는 기타 특성을 통해 리소스를 식별하는 간단한 형식의 문자열입니다.

<br>

#### 3.2.1 General Syntax

> 일반 구문

<br>

URIs in HTTP can be represented in absolute form or relative to some known base URI [11], depending upon the context of their use.
The two forms are differentiated by the fact that absolute URIs always begin with a scheme name followed by a colon.
For definitive information on URL syntax and semantics, see "Uniform Resource Identifiers (URI): Generic Syntax and Semantics," RFC 2396 [42] (which replaces RFCs 1738 [4] and RFC 1808 [11]).
This specification adopts the definitions of "URI-reference", "absoluteURI", "relativeURI", "port", "host","abs_path", "rel_path", and "authority" from that specification.

The HTTP protocol does not place any a priori limit on the length of a URI.
Servers MUST be able to handle the URI of any resource they serve, and SHOULD be able to handle URIs of unbounded length if they provide GET-based forms that could generate such URIs.
A server SHOULD return 414 (Request-URI Too Long) status if a URI is longer than the server can handle (see section 10.4.15).

Note: Servers ought to be cautious about depending on URI lengths above 255 bytes, because some older client or proxy implementations might not properly support these lengths.

> HTTP 의 URI 는 사용되는 문맥에 따라서 절대 경로나 base URI의 상대경로로 표현할 수 있습니다.
> 두 형식은 절대경로 URI 가 항상 스키마 이름으로 시작하고 콜론이 뒤따른다는 점에서 차이가 있습니다.
> URL 구문과 의미에 대한 자세한 정보는 RFC 2396 의 "Uniform Resource Identifiers (URI): Generic Syntax and Semantics," 를 참고하십시오.
> 이 스펙은 해당 스펙의 URI-reference, absoluteURI, relativeURI, port, host, abs_path, rel_path, authority 정의들을 사용합니다.
> 
> HTTP 프로토콜은 URI 길이에 저한을 두지 않습니다.
> 서버는 반드시 어떤 리소스의 URI든 처리할 수 있어야하고, 그들이 GET 기반의 폼을 URI를 통해 전달했을때 무제한 길이의 URI를 처리할 수 있는것이 권장됩니다.
> 만약 서버가 처리할 수 있는 URI 길이를 초과하는 경우, 서버는 414 (요청 URI 가 너무 김) 에러를 반환해야 합니다.
> 
> 참고: 일부 구형 클라이언트와 프록시 구현은 255 바이트를 넘어가는 URI 길이를 처리하지 못하므로, 서버는 255바이트 이상의 URI 길이를 사용할 땐 주의해야 합니다.

<br>

#### 3.2.2 http URL

> http URL

<br>

The "http" scheme is used to locate network resources via the HTTP protocol.
This section defines the scheme-specific syntax and semantics for http URLs.

```
 http_URL = "http:" "//" host [ ":" port ] [ abs_path [ "?" query ]]
```

If the port is empty or not given, port 80 is assumed.
The semantics are that the identified resource is located at the server listening for TCP connections on that port of that host, and the Request-URI for the resource is abs_path (section 5.1.2).
The use of IP addresses in URLs SHOULD be avoided whenever possible (see RFC 1900 [24]).
If the abs_path is not present in the URL, it MUST be given as "/" when used as a Request-URI for a resource (section 5.1.2).
If a proxy receives a host name which is not a fully qualified domain name, it MAY add its domain to the host name it received.
If a proxy receives a fully qualified domain name, the proxy MUST NOT change the host name.


> http 스키마는 HTTP 프로토콜을 통해서 네트워크 리소스를 찾는 데 사용됩니다.
> 이 챕터에선 http URL에 대한 스키마별 구문과 의미를 정의합니다.
> 
> (코드 생략)
> 
> 만약 포트가 비어있거나 지정되지 않은 경우, 포트 80을 사용합니다.
> 의미는 식별된 리소스가 해당 호스트의 해당 포트에서 TCP 연결을 수신하는 서버에 있으며, 리소스에 대한 요청 URI는 abs_path 라는 것입니다.
> URL에 IP 주소를 사용하는 것은 가능한 피해야 합니다. (RFC 1900 참고) 
> 만약 abs_path 가 현재 URL에 없는 경우, 요청 URI를 사용할 때는 반드시 "/" 로 지정해야 합니다.
> 만약 프록시가 도메인 이름으로는 완벽하지 않은 호스트 이름을 수신한다면, 수신한 메시지에 자신의 도메인을 호스트 이름에 추가할 수 있습니다. 
> 만약 프록시가 완벽한 도메인 이름을 수신한다면, 프록시는 호스트 이름을 변경해서는 안됩니다.

<br>

#### 3.2.3 URI Comparison

> URI 비교

<br>

When comparing two URIs to decide if they match or not, a client SHOULD use a case-sensitive octet-by-octet comparison of the entire URIs, with these exceptions:
- A port that is empty or not given is equivalent to the default port for that URI-reference;
- Comparisons of host names MUST be case-insensitive;
- Comparisons of scheme names MUST be case-insensitive;
- An empty abs_path is equivalent to an abs_path of "/".

Characters other than those in the "reserved" and "unsafe" sets (see RFC 2396 [42]) are equivalent to their ""%" HEX HEX" encoding.

For example, the following three URIs are equivalent:
```
      http://abc.com:80/~smith/home.html
      http://ABC.com/%7Esmith/home.html
      http://ABC.com:/%7esmith/home.html
```

> 만약 두 개의 URI를 비교하여 일치여부를 결정한다면, 클라이언트는 다음과 같은 예외를 제외하고 전체 URI의 대소문자를 구별하는 옥텟 단위 비교를 사용해야 합니다.
> - 포트가 비어있거나 지정되지 않은 경우 해당 URI 참조의 기본 포트와 동일합니다.
> - 호스트 이름 비교는 대소문자를 구별하지 않아야 합니다.
> - 스키마 이름 비교는 대소문자를 구별하지 않아야 합니다.
> - 비어있는 abs_path 는 "/" abs_path 와 동일합니다.
> 
> 예약되거나 안전하지 않은 문자 집합 (RFC 2396) 은 %16진수 인코딩과 동일합니다.
> 
> 예를 들어, 다음 3개의 URI는 동일합니다.
> 
> (코드 생략)

<br>

### 3.3 Date/Time Formats

> 날짜/시간 형식

<br>

#### 3.3.1 Full Date

> 전체 날짜

<br>

HTTP applications have historically allowed three different formats for the representation of date/time stamps:

```
      Sun, 06 Nov 1994 08:49:37 GMT  ; RFC 822, updated by RFC 1123
      Sunday, 06-Nov-94 08:49:37 GMT ; RFC 850, obsoleted by RFC 1036
      Sun Nov  6 08:49:37 1994       ; ANSI C's asctime() format
```

The first format is preferred as an Internet standard and represents a fixed-length subset of that defined by RFC 1123 [8] (an update to RFC 822 [9]).
The second format is in common use, but is based on the obsolete RFC 850 [12] date format and lacks a four-digit year.
HTTP/1.1 clients and servers that parse the date value MUST accept all three formats (for compatibility with HTTP/1.0), though they MUST only generate the RFC 1123 format for representing HTTP-date values in header fields.
See section 19.3 for further information.

Note: Recipients of date values are encouraged to be robust in accepting date values that may have been sent by non-HTTP applications, as is sometimes the case when retrieving or posting messages via proxies/gateways to SMTP or NNTP.

All HTTP date/time stamps MUST be represented in Greenwich Mean Time (GMT), without exception.
For the purposes of HTTP, GMT is exactly equal to UTC (Coordinated Universal Time).
This is indicated in the first two formats by the inclusion of "GMT" as the three-letter abbreviation for time zone, and MUST be assumed when reading the asctime format.
HTTP-date is case sensitive and MUST NOT include additional LWS beyond that specifically included as SP in the grammar.

```
       HTTP-date    = rfc1123-date | rfc850-date | asctime-date
       rfc1123-date = wkday "," SP date1 SP time SP "GMT"
       rfc850-date  = weekday "," SP date2 SP time SP "GMT"
       asctime-date = wkday SP date3 SP time SP 4DIGIT
       date1        = 2DIGIT SP month SP 4DIGIT
                      ; day month year (e.g., 02 Jun 1982)
       date2        = 2DIGIT "-" month "-" 2DIGIT
                      ; day-month-year (e.g., 02-Jun-82)
       date3        = month SP ( 2DIGIT | ( SP 1DIGIT ))
                      ; month day (e.g., Jun  2)
       time         = 2DIGIT ":" 2DIGIT ":" 2DIGIT
                      ; 00:00:00 - 23:59:59
       wkday        = "Mon" | "Tue" | "Wed"
                    | "Thu" | "Fri" | "Sat" | "Sun"
       weekday      = "Monday" | "Tuesday" | "Wednesday"
                    | "Thursday" | "Friday" | "Saturday" | "Sunday"
       month        = "Jan" | "Feb" | "Mar" | "Apr"
                    | "May" | "Jun" | "Jul" | "Aug"
                    | "Sep" | "Oct" | "Nov" | "Dec"
```

Note: HTTP requirements for the date/time stamp format apply only to their usage within the protocol stream.
Clients and servers are not required to use these formats for user presentation, request logging, etc.

> HTTP 애플리케이션은 지금까지 날짜/시간 표현을 위해 세 가지 형식을 허용해 왔습니다.
> 
> (코드 생략)
> 
> 첫 번째 형식은 인터넷 표준으로 선호되며 RFC 1123의 정의되어 있는 고정 길이 하위 집합을 나타냅니다.
> 두 번째 형식은 일반적으로 사용되지만, 이제는 사용되지 않는 RFC 850을 기반으로 하는 날짜 형식으로, 4자리 연도가 없습니다.
> 날짜 형식을 분석하는 HTTP/1.1 클라이언트와 서버는 세가지 형식을 모두 허용하되 (HTTP/1.0 과의 호환성을 위해), 헤더 필드에 값을 표현할때는 RFC 1123 형식으로만 생성해야 합니다.   
> 자세한 내용은 19.3 섹션을 참고하십시오.
>
> 참고: 날짜값의 수신자는 프록시/게이트웨이의 SMTP 나 NNTP 프로토콜로 메시지를 송수신할 수 있으므로, HTTP 가 아닌 애플리케이션이 전송한 날짜값도 허용하는 것이 좋습니다.
> 
> 모든 HTTP 날짜/시간 표현은 예외없이 그리니치 표준시로 표시해야 합니다.
> HTTP의 목적 상, GMT 는 UTC (협정 세계시)와 동일합니다.
> 처음 두 형식에는 그리니치 천문시의 약자로 GMT가 포함되어 표시되며, `asctime` 형식을 읽을 때도 그리니치 천문시 기준이라 간주해야 합니다.
> HTTP 날짜는 대소문자를 구별하며, 문법상 특별히 SP가 포함되는 것을 제외하고는 LWS를 절대 추가해선 안됩니다. 
> 
> (코드 생략)
> 
> 참고: 날짜/시간 형식에 대한 HTTP 요구사항은 프로토콜 스트림내에서 사용되는 경우에만 적용됩니다.
> 클라이언트와 서버는 사용자 표현이나 요청 로깅과 같은 동작에 이러한 형식을 사용할 필요가 없습니다.

<br>

#### 3.3.2 Delta Seconds

> 시간 변화

<br>

Some HTTP header fields allow a time value to be specified as an integer number of seconds, represented in decimal, after the time that the message was received.

`delta-seconds  = 1*DIGIT`

> 일부 HTTP 헤더 필드는 메시지를 받고 난 이후의 시간 값을 십진수 정수로 표현한 초를 허용합니다.

<br>

### 3.4 Character Sets

> 문자 집합

<br>

HTTP uses the same definition of the term "character set" as that described for MIME:
The term "character set" is used in this document to refer to a method used with one or more tables to convert a sequence of octets into a sequence of characters.
Note that unconditional conversion in the other direction is not required, in that not all characters may be available in a given character set and a character set may provide more than one sequence of octets to represent a particular character.
This definition is intended to allow various kinds of character encoding, from simple single-table mappings such as US-ASCII to complex table switching methods such as those that use ISO-2022's techniques.
However, the definition associated with a MIME character set name MUST fully specify the mapping to be performed from octets to characters.
In particular, use of external profiling information to determine the exact mapping is not permitted.

Note: This use of the term "character set" is more commonly referred to as a "character encoding."
However, since HTTP and MIME share the same registry, it is important that the terminology also be shared.

HTTP character sets are identified by case-insensitive tokens.
The complete set of tokens is defined by the IANA Character Set registry [19].

```
charset = token
```

Although HTTP allows an arbitrary token to be used as a charset value, any token that has a predefined value within the IANA Character Set registry [19] MUST represent the character set defined by that registry.
Applications SHOULD limit their use of character sets to those defined by the IANA registry.

Implementors should be aware of IETF character set requirements [38] [41].

> HTTP 는 MIME에 대해 "문자 집합" 과 동일한 용어 정의를 사용합니다.
> "문자 집합" 이라는 용어는 하나 이상의 테이블에서 옥텟 시퀸스를 문자 시퀸스로 변환하는 데 사용되는 메서드를 가리키는데 사용됩니다.
> 주어진 문자 집합에서 모든 문자를 사용할 수 있는 것은 아니며 문자 집합이 특정 문자를 표현하는 옥텟 시퀸스를 2개 이상 제공할 수 있으므로 무조건 다른 방향으로 변환할 필요는 없습니다.
> 이 정의는 US-ASCII와 같은 간단한 단일 테이블 매핑부터 ISO-2022의 기술을 사용하는 복잡한 테이블 전환 방법까지 사용하여 다양한 종류의 문자 인코딩을 허용하기 위함입니다.
> 하지만, MIME 문자 집합과 연관된 정의는 옥텟에서 문자로 변환하는 매핑을 완전히 지정해야 합니다.
> 특히, 정확한 매핑을 결정하기 위해 외부 프로파일링 정보를 사용하는 것은 허용되지 않습니다.
>
> 참고: "문자 집합" 이란 용어의 사용은 "문자열 인코딩" 이란 용어로 더욱 흔하게 사용됩니다.
> 하지만 HTTP와 MIME이 같은 레지스트리를 공유하므로, 용어 역시 공유하는것이 중요합니다.
> 
> HTTP 문자 집합은 대소문자를 구별하지 않는 토큰에 의해 식별됩니다.
> 전체 토큰 집합은 IANA 문자 집합 레지스트리에 정의되어 있습니다.
> 
> (코드 생략)
> 
> HTTP에서는 임의의 토큰을 문자 집합 값으로 사용할 수 있지만, IANA 문자 집합 레지스트리에 사전 정의된 값을 가진 토큰은 반드시 레지스트리에 정의된 문자 집합을 나타내야 합니다.
> 애플리케이션은 문자 집합의 사용을 IANA 레지스트리에 등록된 문자 집합으로 제한해야 합니다.
> 
> 구현자는 IETF 문자 집합 요구사항을 알고 있어야 합니다.

<br>

#### 3.4.1 Missing Charset

> 누락된 문자 집합

<br>

Some HTTP/1.0 software has interpreted a Content-Type header without charset parameter incorrectly to mean "recipient should guess."
Senders wishing to defeat this behavior MAY include a charset parameter even when the charset is ISO-8859-1 and SHOULD do so when it is known that it will not confuse the recipient.

Unfortunately, some older HTTP/1.0 clients did not deal properly with an explicit charset parameter.
HTTP/1.1 recipients MUST respect the charset label provided by the sender; and those user agents that have a provision to "guess" a charset MUST use the charset from the content-type field if they support that charset, rather than the recipient's preference, when initially displaying a document.
See section 3.7.1.

> 일부 HTTP/1.0 소프트웨어는 문자 집합 파라미터가 없는 `Content-Type` 헤더를 "수신자가 알잘딱깔센 추측해야 한다." 는 의미로 잘못 해석했습니다.
> 이러한 동작을 방지하려는 발신자는 문자 집합이 ISO-8859-1 이더라도 문자 집합 파라미터를 포함할 수 있으며, 수신자에게 혼란을 주지 않음이 확실시되어야만 포함할 수 있습니다.
> 
> 그러나 비극적이게도, 일부 오래된 HTTP/1.0 클라이언트들은 명시되어 있는 문자 집합 파라미터를 적절히 처리하지 못했습니다.
> HTTP/1.1 수신자는 반드시 전송자가 제공한 문자 집합 라벨을 존중해야 하고, 문자 집합을 추측하는 클라이언트들은 `content-type` 필드의 문자 집합을 지원할 수 있다면 반드시 사용해야 합니다. 
> 섹션 3.7.1 을 참고하십시오.

<br>

### 3.5 Content Codings

> 콘텐츠 코딩

<br>

Content coding values indicate an encoding transformation that has been or can be applied to an entity.
Content codings are primarily used to allow a document to be compressed or otherwise usefully transformed without losing the identity of its underlying media type and without loss of information.
Frequently, the entity is stored in coded form, transmitted directly, and only decoded by the recipient.

```
content-coding   = token
```

All content-coding values are case-insensitive.
HTTP/1.1 uses content-coding values in the Accept-Encoding (section 14.3) and Content-Encoding (section 14.11) header fields.
Although the value describes the content-coding, what is more important is that it indicates what decoding mechanism will be required to remove the encoding.

The Internet Assigned Numbers Authority (IANA) acts as a registry for content-coding value tokens.
Initially, the registry contains the following tokens:

`gzip`
An encoding format produced by the file compression program "gzip" (GNU zip) as described in RFC 1952 [25].
This format is a Lempel-Ziv coding (LZ77) with a 32 bit CRC.

`compress`
The encoding format produced by the common UNIX file compression program "compress".
This format is an adaptive Lempel-Ziv-Welch coding (LZW).

Use of program names for the identification of encoding formats is not desirable and is discouraged for future encodings.
Their use here is representative of historical practice, not good design.
For compatibility with previous implementations of HTTP, applications SHOULD consider "x-gzip" and "x-compress" to be equivalent to "gzip" and "compress" respectively.

`deflate`
The "zlib" format defined in RFC 1950 [31] in combination with the "deflate" compression mechanism described in RFC 1951 [29].

`identity`
The default (identity) encoding;
the use of no transformation whatsoever.
This content-coding is used only in the Accept-Encoding header, and SHOULD NOT be used in the Content-Encoding header.

New content-coding value tokens SHOULD be registered;
to allow interoperability between clients and servers, specifications of the content coding algorithms needed to implement a new value SHOULD be publicly available and adequate for independent implementation, and conform to the purpose of content coding defined in this section.

> 콘텐츠 코딩 값은 엔티티에 적용되었거나 적용할 수 있는 인코딩 변환을 나타냅니다. 
> 콘텐츠 코딩은 주로 근본적인 미디어 타입의 정체성을 잃지 않고, 정보 손실 없이 문서를 압축하거나 변환하는데 사용됩니다.
> 엔티티는 종종 코드 형태로 저장되어 직접 전송되고, 수신자만 이를 해독할 수 있습니다.
> 
> (코드 생략)
> 
> 모든 콘텐츠 코딩 값은 대소문자를 구별하지 않습니다.
> HTTP/1.1 은 Accept-Encoding 과 Content-Encoding 헤더 필드값의 콘텐츠 코딩값을 사용합니다.
> 이 값은 콘텐츠 코딩을 설명하지만, 더욱 중요한 것은 이것이 해독 메커니즘을 알려준다는 것입니다.
> 
> 인터넷 번호 할당 기관인 (IANA) 는 콘텐츠 코딩 값 토큰을 위한 레지스트리 역할을 합니다.
> 처음엔, 레지스트리는 다음 토큰들을 포함합니다.
> 
> `gzip`
> `gzip` 파일 압축 프로그램에 의해 만들어지는 인코딩 형식입니다.
> 이 형식은 32 bit CRC를 사용하는 Lempel-Ziv 코딩 형식입니다.
> 
> `compress`
> 일반적인 UNIX 파일 압축 프로그램인 "compress" 에 의해 만들어지는 인코딩 형식입니다.
> 이 형식은 적응형 Lempel-Ziv-Welch 코딩 형식입니다.
> 
> 인코딩 형식을 식별하기위해 프로그램 명을 쓰는 것은 바람직하지 않고, 향후 인코딩에 사용하지 않는 것이 좋습니다.
> 그것들의 사용은 좋은 디자인이 아닌 역사적 관행을 대표합니다.
> 이전 HTTP 구현체와의 호환성을 위해, 애플리케이션은 "x-gzip" 이나 "x-compress" 을 각각 "gzip" 이나 "compress" 와 동등한 것으로 간주해야 합니다.
> 
> `deflate`
> `zlib` 형식과 `deflate` 압축 메커니즘이 결합된 형식입니다.
> 
> `identity`
> 기본 인코딩입니다. 
> 변환을 전혀 사용하지 않습니다.
> 이 콘텐츠 코딩은 `Accept-Encoding` 헤더에서만 사용되고, `Content-Encoding` 헤더에는 사용할 수 없습니다.
> 
> 새로운 콘텐츠 코딩 값 토큰은 반드시 등록되어야 합니다.
> 클라이언트와 서버 간의 상호 운용성을 허용하려면 새로운 값을 구현하는 데 필요한 콘텐츠 코딩 알고리즘의 사양이 공개적으로 사용 가능하고 독립적으로 구현하기에 적합해야 하며 이 섹션에 정의된 콘텐츠 코딩의 목적에 부합해야 합니다.

<br>

### 3.6 Transfer Codings

> 전송 코딩

<br>

Transfer-coding values are used to indicate an encoding transformation that has been, can be, or may need to be applied to an entity-body in order to ensure "safe transport" through the network.
This differs from a content coding in that the transfer-coding is a property of the message, not of the original entity.

```java
       transfer-coding         = "chunked" | transfer-extension
       transfer-extension      = token *( ";" parameter )
```

Parameters are in  the form of attribute/value pairs.

```java
      parameter               = attribute "=" value
       attribute               = token
       value                   = token | quoted-string
```

All transfer-coding values are case-insensitive.
HTTP/1.1 uses transfer-coding values in the TE header field (section 14.39) and in the Transfer-Encoding header field (section 14.41).

Whenever a transfer-coding is applied to a message-body, the set of transfer-codings MUST include "chunked", unless the message is terminated by closing the connection.
When the "chunked" transfer-coding is used, it MUST be the last transfer-coding applied to the message-body.
The "chunked" transfer-coding MUST NOT be applied more than once to a message-body.
These rules allow the recipient to determine the transfer-length of the message (section 4.4).

Transfer-codings are analogous to the Content-Transfer-Encoding values of MIME [7], which were designed to enable safe transport of binary data over a 7-bit transport service.
However, safe transport has a different focus for an 8bit-clean transfer protocol.
In HTTP, the only unsafe characteristic of message-bodies is the difficulty in determining the exact body length (section 7.2.2), or the desire to encrypt data over a shared transport.

The Internet Assigned Numbers Authority (IANA) acts as a registry for transfer-coding value tokens.
Initially, the registry contains the following tokens: "chunked" (section 3.6.1), "identity" (section 3.6.2), "gzip" (section 3.5), "compress" (section 3.5), and "deflate" (section 3.5).

New transfer-coding value tokens SHOULD be registered in the same way as new content-coding value tokens (section 3.5).

A server which receives an entity-body with a transfer-coding it does not understand SHOULD return 501 (Unimplemented), and close the connection.
A server MUST NOT send transfer-codings to an HTTP/1.0 client.

> `Transfer-coding` 값은 네트워크를 통한 '안전한 전송'을 보장하기 위해 엔티티 바디에 적용되었거나, 적용될 수 있거나, 적용해야 할 수 있는 인코딩 변환을 나타내는 데 사용됩니다.
> 이는 전송 코딩이 원본 엔티티가 아닌 메시지의 속성이라는 점에서 콘텐츠 코딩과 다릅니다.
> 
> (코드 생략)
> 
> 파라미터는 속성/값의 형태로 쌍을 이룹니다.
> 
> (코드 생략)
> 
> 모든 전송 코딩 값은 대소문자를 구별하지 않습니다.
> HTTP/1.1 은 TE 헤더 필드 와 `Transfer-Encoding` 헤더에 전송 코딩 값을 담아 사용합니다.
> 
> 전송 코딩이 메시지 바디에 적용되었다면, 커넥션 종료에 의해 메시지가 종료되지 않는 한 전송 코딩 집합들은 반드시 "chunked" 를 포함해야 합니다. 
> 만약 "chunked" 전송 코딩값이 사용되었다면, 메시지 본문에 적용된 마지막 전송 코딩이어야 합니다.
> "chunked" 전송 코딩은 메시지 바디에서 한 번 이상 적용되어서는 안됩니다.
> 이러한 규칙들을 통해 수신자가 메시지의 전송 길이를 결정할 수 있습니다.
>
> 전송 코딩은 7비트 전송 서비스를 통해 이진 데이터를 안전하게 전송할 수 있도록 설계된 MIME의 `Content-Transfer-Encoding` 값과 유사합니다.
> 하지만, 안전한 전송은 8bit-clean 전송 프로토콜과는 다른 초점을 갖고 있습니다.
> HTTP 에서, 메시지 본문에서 유일하게 위험한 특성은 정확한 본문 길이를 결정하기 어렵다는 것과 공유 전송을 통해 데이터를 암호화하기 어렵다는 것입니다.
> 
> IANA 는 전송 코딩 값 토큰의 레지스트리 역할을 합니다.
> 처음엔 레지스트리에 "chunked", "identity", "gzip", "compress", "deflate" 토큰들이 등록되어 있었습니다.
> 
> 새로운 전송 코딩 값 토큰은 새로운 콘텐츠 코딩 값 토큰과 같은 방식으로 등록해야 합니다.
> 
> 이해할 수 없는 전송 코딩을 포함한 엔티티 본문을 받은 서버는 501 에러 (구현되지 않음)를 반환하고, 커넥션을 종료해야 합니다.
> 서버는 HTTP/1.0 클라이언트에게 전송 코딩을 보내서는 안됩니다.

<br>

#### 3.6.1 Chunked Transfer Coding

> 청크 전송 코딩

<br>

The chunked encoding modifies the body of a message in order to transfer it as a series of chunks, each with its own size indicator, followed by an OPTIONAL trailer containing entity-header fields.
This allows dynamically produced content to be transferred along with the information necessary for the recipient to verify that it has received the full message.

```java
       Chunked-Body   = *chunk
                        last-chunk
                        trailer
                        CRLF

       chunk          = chunk-size [ chunk-extension ] CRLF
                        chunk-data CRLF
       chunk-size     = 1*HEX
       last-chunk     = 1*("0") [ chunk-extension ] CRLF

       chunk-extension= *( ";" chunk-ext-name [ "=" chunk-ext-val ] )
       chunk-ext-name = token
       chunk-ext-val  = token | quoted-string
       chunk-data     = chunk-size(OCTET)
       trailer        = *(entity-header CRLF)
```

The chunk-size field is a string of hex digits indicating the size of the chunk.
The chunked encoding is ended by any chunk whose size is zero, followed by the trailer, which is terminated by an empty line.

The trailer allows the sender to include additional HTTP header fields at the end of the message.
The Trailer header field can be used to indicate which header fields are included in a trailer (see section 14.40).

A server using chunked transfer-coding in a response MUST NOT use the trailer for any header fields unless at least one of the following is true:

a) the request included a TE header field that indicates "trailers" is acceptable in the transfer-coding of the  response, as described in section 14.39; or,

b) the server is the origin server for the response, the trailer fields consist entirely of optional metadata, and the recipient could use the message (in a manner acceptable to the origin server) without receiving this metadata.
In other words, the origin server is willing to accept the possibility that the trailer fields might be silently discarded along the path to the client.

This requirement prevents an interoperability failure when the message is being received by an HTTP/1.1 (or later) proxy and forwarded to an HTTP/1.0 recipient.
It avoids a situation where compliance with the protocol would have necessitated a possibly infinite buffer on the proxy.

An example process for decoding a Chunked-Body is presented in appendix 19.4.6.

All HTTP/1.1 applications MUST be able to receive and decode the "chunked" transfer-coding, and MUST ignore chunk-extension extensions they do not understand.

> 청크 인코딩은 메시지 본문을 수정하여 각각의 고유한 크기 표시 지표가 있는 일련의 청크로 전송한 다음, 엔티티 헤더 필드를 포함하는 선택적 트레일러로 전송합니다.
> 이를 통해 동적으로 생성된 콘텐츠를 수신자가 전체 메시지를 수신했는지 확인하는데 필요한 정보와 함께 전송할 수 있습니다.
> 
> (코드 생략)
> 
> chunk-size 필드는 청크의 크기를 나타내는 16진수 문자열입니다.
> 청크 인코딩은 크기가 0인 청크로 끝나고, 그 뒤에 빈줄로 끝나는 트레일러로 끝납니다.
> 
> 트레일러를 사용하면 전송자가 메시지의 끝에 추가적인 HTTP 헤더 필드를 포함할 수 있습니다.
> 트레일러 헤더 필드는 트레일러에 포함된 헤더 필드를 표시하는 데 사용할 수 있습니다. (섹션 14.40을 참고하십시오.)
> 
> 응답으로 청크 전송 코딩을 사용하는 서버는 다음 중 한가지라도 해당사항이 없다면 헤더 필드에 트레일러를 사용해서는 안됩니다.
> 
> a) 섹션 14.39에 설명된 대로, 응답의 전송 코딩에 "trailers" 가 허용됨을 나타내는 TE 헤더 필드가 포함된 경우
> 
> b) 서버가 응답의 origin 서버이고, 트레일러 필드가 선택적인 메타데이터로만 구성되며, 수신자가 이러한 메타데이터를 받지 않고도 메시지를 사용할 수 있는 경우 (origin 서버가 허용하는 방식)
> 즉, origin 서버는 트레일러 필드가 클라이언트로 가다가 자동으로 삭제될 가능성을 허용합니다.
> 
> 이러한 요구사항들은 HTTP/1.1 (혹은 이상의) 프록시가 메시지를 받아 HTTP/1.0 수신자로 전달할 때 상호 운용의 실패를 막습니다. 
> 이를 통해, 프로토콜을 준수하기 위해 프록시에 무한대의 버퍼가 필요한 상황을 피할 수 있습니다.
> 
> "Chunked-Body" 를 해독하는 예제 프로세스는 부록 19.4.6 에 있습니다. 
> 
> 모든 HTTP/1.1 애플리케이션들은 반드시 "chunked" 전송 코딩을 수신하고 해독할 수 있어야하고, 이해할 수 없는 "chunk-extension" 확장들은 무시해야 합니다.

<br>

### 3.7 Media Types

> 미디어 타입

<br>

HTTP uses Internet Media Types [17] in the Content-Type (section 14.17) and Accept (section 14.1) header fields in order to provide open and extensible data typing and type negotiation.

```java
       media-type     = type "/" subtype *( ";" parameter )
       type           = token
       subtype        = token
```

Parameters MAY follow the type/subtype in the form of attribute/value pairs (as defined in section 3.6).

The type, subtype, and parameter attribute names are case-insensitive.
Parameter values might or might not be case-sensitive, depending on the semantics of the parameter name.
Linear white space (LWS) MUST NOT be used between the type and subtype, nor between an attribute and its value.
The presence or absence of a parameter might be significant to the processing of a media-type, depending on its definition within the media type registry.

Note that some older HTTP applications do not recognize media type parameters.
When sending data to older HTTP applications, implementations SHOULD only use media type parameters when they are required by that type/subtype definition.

Media-type values are registered with the Internet Assigned Number Authority (IANA [19]).
The media type registration process is outlined in RFC 1590 [17].
Use of non-registered media types is discouraged.

> 개방적이고 확장가능한 데이터 유형과 유형 협상을 제공하기 위해서 `Content-Type` 과 `Accept` 헤더 필드에서 HTTP는 인터넷 미디어 타입을 사용합니다.  
> 
> (코드 생략)
> 
> 파라미터는 속성/값 쌍의 형태로 type/subtype 을 따를 수 있습니다. (섹션 3.6에 정의된대로)
> 
> 타입, 하위 타입, 그리고 파라미터 속성의 이름들은 대소문자를 구분하지 않습니다.
> 파라미터 값들은 파라미터 이름의 의미에 따라 대소문자를 구분할 수도, 구분하지 않을 수도 있습니다.
> 타입과 하위 타입 사이, 속성과 해당 값 사이에 선형 공백 (LWS)을 사용해선 안됩니다.  
> 파라미터의 존재 여부는 미디어 타입 레지스트리에 정의된 정의에 따라 미디어 타입을 처리하는데 중요할 수 있습니다. 
> 
> 일부 오래된 HTTP 애플리케이션은 미디어 타입 파라미터를 인식하지 못한다는 점에 유의하십시오.
> 구형 HTTP 애플리케이션에 데이터를 보낼때, 구현은 해당 타입/서브타입 정의에서 요구하는 경우에만 미디어 타입 파라미터를 사용해야 합니다. 
> 
> 미디어 타입 값들은 IANA 에 등록됩니다.
> 미디어 타입 등록 프로세스는 RFC 1590에서 설명합니다.
> 등록되지 않은 미디어 타입 사용은 권장하지 않습니다.

<br>

#### 3.7.1 Canonicalization and Text Defaults

> 정규화 및 텍스트 기본값

Internet media types are registered with a canonical form.
An entity-body transferred via HTTP messages MUST be represented in the appropriate canonical form prior to its transmission except for "text" types, as defined in the next paragraph.

When in canonical form, media subtypes of the "text" type use CRLF as the text line break.
HTTP relaxes this requirement and allows the transport of text media with plain CR or LF alone representing a line break when it is done consistently for an entire entity-body.
HTTP applications MUST accept CRLF, bare CR, and bare LF as being representative of a line break in text media received via HTTP.
In addition, if the text is represented in a character set that does not use octets 13 and 10 for CR and LF respectively, as is the case for some multi-byte character sets, HTTP allows the use of whatever octet sequences are defined by that character set to represent the equivalent of CR and LF for line breaks.
This flexibility regarding line breaks applies only to text media in the entity-body;
a bare CR or LF MUST NOT be substituted for CRLF within any of the HTTP control structures (such as header fields and multipart boundaries).

If an entity-body is encoded with a content-coding, the underlying data MUST be in a form defined above prior to being encoded.

The "charset" parameter is used with some media types to define the character set (section 3.4) of the data.
When no explicit charset parameter is provided by the sender, media subtypes of the "text" type are defined to have a default charset value of "ISO-8859-1" when received via HTTP.
Data in character sets other than "ISO-8859-1" or its subsets MUST be labeled with an appropriate charset value.
See section 3.4.1 for compatibility problems.

> 인터넷 미디어 타입은 표준 양식으로 등록됩니다.
> HTTP 메시지를 통해 전달되는 엔티티 본문은 다음 단락에 정의된 대로 `text` 타입을 제외하고는, 전송 전에 적절한 표준 형식으로 표현되어야 합니다. 
> 
> 표준형식인 경우, `text` 의 하위 미디어 타입은 줄 바꿈으로 CRLF를 사용합니다.
> HTTP는 이 요구사항을 완화하여, 전체 엔티티 본문이 일관적이라면 CR 혹은 LF 만 있는 줄 바꿈 표현 방식을 사용한 텍스트 미디어를 전송할 수 있도록 허용합니다. 
> HTTP 애플리케이션은 HTTP를 통해 수신된 텍스트 미디어에서 CRLF, CR이나 LF가 줄 바꿈을 표현하는 경우만을 허용합니다.
> 또한 일부 멀티바이트 문자 집합처럼 옥텟 13과 10이 CR, LF로 사용되지 않는 경우, HTTP는 줄 바꿈에 해당하는 CR 과 LF를 표현하는 어떤 옥텟 시퀸스던지 상관없이 사용할 수 있도록 허용합니다.
> 이러한 줄바꿈에 대한 유연성은 오직 엔티티 본문에 사용된 텍스트 미디어에만 적용됩니다.
> 헤더 필드나 멀티파트 경계와 같은 HTTP 제어 구조 내에서는 단독의 CR 혹은 LF를 CRLF로 대체해서는 안됩니다.
> 
> 만약 엔티티 본문이 콘텐츠 코딩으로 인코딩된 경우, 기본 데이터는 인코딩되기 전에 위에 정의된 형식이어야 합니다.
> 
> `charset` 파라미터는 일 부 미디어 타입과 함께 데이터의 문자 집합 (섹션 3.4) 을 정의하는 데 사용됩니다.
> 발신자가 명시적으로 문자 집합 파라미터를 제공하지 않은 경우, `text` 타입의 미디어 하위 타입은 HTTP 메시지의 기본 문자 집합인 ISO-8859-1을 사용합니다.
> ISO-8859-1 또는 그 하위 집합이 아닌 문자 집합을 사용하는 데이터는 반드시 적절한 문자 집합 값을 라벨링해야 합니다.
> 호환성 문제는 섹션 3.4.1 을 참조하십시오.

<br>

#### 3.7.2 Multipart Types

> 멀티파트 타입

MIME provides for a number of "multipart" types -- encapsulations of one or more entities within a single message-body.
All multipart types share a common syntax, as defined in section 5.1.1 of RFC 2046[40], and MUST include a boundary parameter as part of the media type value.
The message body is itself a protocol element and MUST therefore use only CRLF to represent line breaks between body-parts.
Unlike in RFC 2046, the epilogue of any multipart message MUST be empty;
HTTP applications MUST NOT transmit the epilogue (even if the original multipart contains an epilogue).
These restrictions exist in order to preserve the self-delimiting nature of a multipart message-body, wherein the "end" of the message-body is indicated by the ending multipart boundary.

In general, HTTP treats a multipart message-body no differently than any other media type: strictly as payload.
The one exception is the "multipart/byteranges" type (appendix 19.2) when it appears in a 206 (Partial Content) response, which will be interpreted by some HTTP caching mechanisms as described in sections 13.5.4 and 14.16.
In all other cases, an HTTP user agent SHOULD follow the same or similar behavior as a MIME user agent would upon receipt of a multipart type.
The MIME header fields within each body-part of a multipart message-body do not have any significance to HTTP beyond that defined by their MIME semantics.

In general, an HTTP user agent SHOULD follow the same or similar behavior as a MIME user agent would upon receipt of a multipart type.
If an application receives an unrecognized multipart subtype, the application MUST treat it as being equivalent to "multipart/mixed".

Note: The "multipart/form-data" type has been specifically defined for carrying form data suitable for processing via the POST request method, as described in RFC 1867 [15].

> MIME 은 다양한 `multipart` 타입들을 제공합니다 -- 하나의 메시지 본문 내에 하나 이상의 엔티티를 캡슐화합니다.
> 모든 멀티파트 타입들은 RFC 2046의 섹션 5.1.1 에 정의된대로 공통 구문을 공유하며, 반드시 경계 파라미터를 미디어 타입의 일부로서 포함해야 합니다.  
> 메시지 본문은 그 자체로 프로토콜 요소이므로 본문 부분 사이의 줄 바꿈을 표현할 땐 반드시 CRLF만 사용해야 합니다.
> RFC 2046과는 다르게, 멀티파트 메시지의 마지막은 반드시 비어있어야 합니다.
> HTTP 애플리케이션은 절대 에필로그를 전송해서는 안됩니다. (원래 멀티파트 메시지가 에필로그를 가지고 있다 하더라도)
> 이러한 제한은 멀티파트 메시지 본문의 자체 구분 특성을 유지하기 위해 존재하며, 메시지 본문의 끝은 다중 파트 경계의 끝으로 표시됩니다.
> 
> 일반적으로, HTTP 는 멀티파트 메시지 본문을 다른 미디어 타입과 동일하게 취급합니다. (동일한 페이로드로서)
> 한가지 예외는 206 (부분 콘텐츠) 응답에 나타나는 `multipart/byteranges` 타입으로 (부록 19.2) 13.5.4와 14.16에 설명된 대로 일부 HTTP 캐싱 메커니즘에 의해 해석됩니다. 
> 그 외의 모든 경우, HTTP 유저 에이전트는 멀티파트 타입을 수신할 때 MIME 에이전트와 같거나 유사한 동작을 따라야 합니다.
> 멀티파트 메시지 본문의 각각의 본문 부분안에 MIME 헤더 필드는 MIME 의미론에 정의된 것 외에는 HTTP 에게 어떤 의미도 갖지 않습니다.
> 
> 일반적으로, HTTP 유저 에이전트는 멀티파트 타입을 수신할 때 MIME 유저 에이전트와 유사하거나 동일한 동작을 따라야 합니다.
> 만약 애플리케이션이 인식할 수 없는 멀티파트 하위 타입을 수신한다면, 애플리케이션은 반드시 `multipart/mixed` 로 처리해야 합니다.
> 
> 참고: `multipart/form-data` 타입은 RFC 1867에 설명된 대로 POST 요청 메서드를 통해 처리하기에 적합한 폼 데이터를 전달하기 위해 특별히 정의되었습니다.

<br>

### 3.8 Product Tokens

> 제품 토큰

<br>

Product tokens are used to allow communicating applications to identify themselves by software name and version.
Most fields using product tokens also allow sub-products which form a significant part of the application to be listed, separated by white space.
By convention, the products are listed in order of their significance for identifying the application.

```java
       product         = token ["/" product-version]
       product-version = token
```

Examples:
```java
       User-Agent: CERN-LineMode/2.15 libwww/2.17b3
       Server: Apache/0.8.4
```

Product tokens SHOULD be short and to the point.
They MUST NOT be used for advertising or other non-essential information.
Although any token character MAY appear in a product-version, this token SHOULD only be used for a version identifier
(i.e., successive versions of the same product SHOULD only differ in the product-version portion of the product value).

> 제품 토큰은 통신하는 애플리케이션들이 소프트웨어 이름과 버전을 사용해 식별할 수 있도록 하는 데 사용됩니다.
> 제품 토큰을 사용하는 대부분의 필드에서는 애플리케이션의 중요한 부분을 구성하는 하위 제품을 공백으로 구분하여 나열할 수 있습니다. 
> 관습적으로, 제품들은 애플리케이션을 식별하는 데 중요한 순서대로 나열됩니다.
> 
> (코드 생략)
> 
> 예시:
> (코드 생략)
> 
> 제품 토큰은 짧고 간결해야 합니다.
> 제품 토큰은 광고나 필요하지 않은 정보에 사용해서는 안됩니다.
> 비록 어떤 토큰 문자열은 제품 버전에 사용될 수 있지만, 이 토큰은 버전 식별자로만 사용되어야 합니다. 
> (즉, 동일한 제품의 후속 버전은 제품 버전 부분만 달라야 합니다.)

<br>

### 3.9 Quality Values

> 품질 가치

<br>

HTTP content negotiation (section 12) uses short "floating point" numbers to indicate the relative importance ("weight") of various negotiable parameters.
A weight is normalized to a real number in the range 0 through 1, where 0 is the minimum and 1 the maximum value.
If a parameter has a quality value of 0, then content with this parameter is `not acceptable' for the client.
HTTP/1.1 applications MUST NOT generate more than three digits after the decimal point.
User configuration of these values SHOULD also be limited in this fashion.

```java
       qvalue         = ( "0" [ "." 0*3DIGIT ] )
                      | ( "1" [ "." 0*3("0") ] )
```

"Quality values" is a misnomer, since these values merely represent relative degradation in desired quality.

> HTTP 콘텐츠 협상은 협상의 중요도를 나타내는 파라미터(가중치)로 짧은 부동 소수점을 사용합니다.
> 가중치는 0 ~ 1 의 실수로 정규화되어있으며, 0이 최소값이고 1이 최대값입니다.
> 만약 파라미터의 값이 0이라면, 이 파라미터의 콘텐츠는 클라이언트가 수용할 수 없습니다.
> HTTP/1.1 애플리케이션은 소수점 이하 3자리수가 넘는 가중치를 만들 수 없습니다.
> 이러한 값의 사용자 설정은 이러한 방식에서 제한됩니다.
> 
> (코드 생략)
> 
> `Quality values`은 원하는 품질의 상대적인 저하를 나타내는 값일 뿐이므로 잘못된 이름입니다.

<br>

### 3.10 Language Tags

> 언어 태그 

<br>

A language tag identifies a natural language spoken, written, or otherwise conveyed by human beings for communication of information to other human beings.
Computer languages are explicitly excluded.
HTTP uses language tags within the Accept-Language and Content-Language fields.

The syntax and registry of HTTP language tags is the same as that defined by RFC 1766 [1].
In summary, a language tag is composed of 1 or more parts:
A primary language tag and a possibly empty series of subtags:

```java
        language-tag  = primary-tag *( "-" subtag )
        primary-tag   = 1*8ALPHA
        subtag        = 1*8ALPHA
```

White space is not allowed within the tag and all tags are case-insensitive.
The name space of language tags is administered by the IANA.
Example tags include:

```java
       en, en-US, en-cockney, i-cherokee, x-pig-latin
```

where any two-letter primary-tag is an ISO-639 language abbreviation and any two-letter initial subtag is an ISO-3166 country code.
(The last three tags above are not registered tags;
all but the last are examples of tags which could be registered in future.)

> 언어 태그는 인간이 다른 인간에게 정보를 전달하기 위해 말하거나, 쓰거나, 다른 방식으로 전달하는 자연 언어를 식별합니다.
> 컴퓨터 언어는 명시적으로 제외됩니다.
> HTTP는 Accept-Language 와 Content-Language 필드 내애서 언어 태그를 사용합니다.
> 
> HTTP 언어 태그의 구문과 레지스트리는 RFC 1766에 정의된 것과 동일합니다.
> 요약하면, 언어 태그는 1개 이상의 부분으로 구성됩니다.:
> 기본 언어 태그와 비어 있을 수 있는 일련의 하위 태그입니다.
> 
> (코드 생략)
> 
> 태그 내에서 선형 공백은 허용되지 않고 모든 태그들은 대소문자를 구별하지 않습니다.
> 언어 태그의 네임 스페이스는 IANA에서 관리합니다.
> 태그의 예는 다음과 같습니다.:
> 
> (코드 생략)
> 
> 두 글자 필수 태그는 ISO-639 언어 축약어이고, 두 글자로 된 하위 태그는 ISO-3166 국가 코드입니다.
> (예제에서 뒤에 있는 3개의 태그들은 등록되지 않은 태그들입니다.;
> 마지막을 제외한 모든 태그는 향후 등록할 수 있는 태그의 예시입니다.)

<br>

### 3.11 Entity Tags

> 엔티티 태그

<br>

Entity tags are used for comparing two or more entities from the same requested resource.
HTTP/1.1 uses entity tags in the ETag (section 14.19), If-Match (section 14.24), If-None-Match (section 14.26), and If-Range (section 14.27) header fields.
The definition of how they are used and compared as cache validators is in section 13.3.3.
An entity tag consists of an opaque quoted string, possibly prefixed by a weakness indicator.

```java
      entity-tag = [ weak ] opaque-tag
      weak       = "W/"
      opaque-tag = quoted-string
```

A "strong entity tag" MAY be shared by two entities of a resource only if they are equivalent by octet equality.

A "weak entity tag," indicated by the "W/" prefix, MAY be shared by two entities of a resource only if the entities are equivalent and could be substituted for each other with no significant change in semantics.
A weak entity tag can only be used for weak comparison.

An entity tag MUST be unique across all versions of all entities associated with a particular resource.
A given entity tag value MAY be used for entities obtained by requests on different URIs.
The use of the same entity tag value in conjunction with entities obtained by requests on different URIs does not imply the equivalence of those entities.


> 엔티티 태그는 동일한 요청 리소에서 두 개 이상의 엔티티들을 비교할 때 사용됩니다.
> HTTP/1.1 은 섹션 14.19의 ETag, 섹션 14.24의 If-Match, 섹션 14.26의 If-None-Match, 섹션 14.27의 If-Range 헤더 필드들을 엔티티 태그로서 사용합니다.
> 캐시 유효성 검증기로서 사용되고 비교되는지는 섹션 13.3.3 에서 설명합니다.
> 엔티티 태그는 불투명한 따옴표로 묶인 문자열로 구성되어 있으며, 약한 태그가 접두사로 붙을 수 있습니다.
> 
> (코드 생략)
> 
> 강한 엔티티 태그는 두 엔티티가 옥텟 동등성에 의해 동등한 경우에만 공유할 수 있습니다.
> 
> 약한 엔티티 태그는 "W/" 접두사로 시작되며, 두 엔티티가 동등하고 의미적으로 큰 변경없이 서로를 대체할 수 있는 경우에만 공유할 수 있습니다.
> 약한 엔티티 태그는 약한 비교에만 사용할 수 있습니다.
> 
> 엔티티 태그는 특정 리소스와 연관된 모든 엔티티의 모든 버전에서 유일해야 합니다. 
> 주어진 엔티티 태그 값은 다른 URI 요청에서 획득한 엔티티에 사용될 수 있습니다. 
> 서로 다른 URI 요청에서 얻은 엔티티들이 동일한 엔티티 태그 값을 사용하더라도, 그 엔티티들의 동등성을 의미하진 않습니다. 

<br>

### 3.12 Range Units

> 범위 단위 

<br>

HTTP/1.1 allows a client to request that only part (a range of) the response entity be included within the response.
HTTP/1.1 uses range units in the Range (section 14.35) and Content-Range (section 14.16) header fields.
An entity can be broken down into subranges according to various structural units.

```java
      range-unit       = bytes-unit | other-range-unit
      bytes-unit       = "bytes"
      other-range-unit = token
```

The only range unit defined by HTTP/1.1 is "bytes".
HTTP/1.1 implementations MAY ignore ranges specified using other units.
HTTP/1.1 has been designed to allow implementations of applications that do not depend on knowledge of ranges.


> HTTP/1.1 클라이언트는 응답 엔티티의 일부(범위)만 응답에 포함하도록 요청할 수 있습니다.
> HTTP/1.1 부록 14.35의 Range 헤더 필드와 부록 14.16의 Content-Range 헤더 필드를 사용하여 범위 단위를 사용합니다.
> 엔티티는 다양한 구조 단위에 따라 하위 범위로 세분화할 수 있습니다.
> 
> (코드 생략)
> 
> HTTP/1.1 에 정의된 유일한 범위 단위는 "bytes" 입니다.
> HTTP/1.1 구현체는 명세된 다른 범위 단위는 무시할 수도 있습니다.
> HTTP/1.1 은 범위에 대한 지식에 의존하지 않는 애플리케이션을 구현할 수 있도록 설계되었습니다.