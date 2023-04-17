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