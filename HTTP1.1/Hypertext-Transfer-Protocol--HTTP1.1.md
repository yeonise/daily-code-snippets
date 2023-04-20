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
