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
> HTTP는 1990년 World-Wide Web global information initiative 에서 사용되었습니다. <br>
> 이 스펙은 HTTP/1.1 프로토콜을 정의하며, 이는 RFC 2068의 업데이트 버전입니다.

<br>

## Introduction
### Purpose
