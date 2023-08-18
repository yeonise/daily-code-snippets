# Introduction

## 이벤트 스트리밍은 무엇인가요?(What is event streaming?)

Event streaming is the digital equivalent of the human body's central nervous system. It is the technological foundation
for the 'always-on' world where businesses are increasingly software-defined and automated, and where the user of
software is more software.

> 이벤트 스트리밍(Event streaming)은 인체의 중추 신경계와 동일한 디지털 방식입니다.
> 이벤트 스트리밍은 비즈니스가 점점 더 소프트웨어 정의 및 자동화되고 소프트웨어 사용자가 더 많은 소프트웨어를 사용하는 'Always-on' 세계를 위한 기술적인 기반입니다.

Technically speaking, event streaming is the practice of capturing data in real-time from event sources like databases,
sensors, mobile devices, cloud services, and software applications in the form of streams of events; storing these event
streams durably for later retrieval; manipulating, processing, and reacting to the event streams in real-time as well as
retrospectively; and routing the event streams to different destination technologies as needed. Event streaming thus
ensures a continuous flow and interpretation of data so that the right information is at the right place, at the right
time.

> 엄밀히 말하면 이벤트 스트리밍은 데이터베이스, 센서, 모바일 기기, 클라우드 서비스, 소프트웨어 애플리케이션과 같은 이벤트 소스로부터
> 이벤트 스트림의 형태로 데이터를 실시간으로 캡처하는 것입니다. 나중에 검색할 수 있도록 이러한 이벤트 스트림을 지속적으로 저장합니다.
> 이벤트 스트림을 실시간으로 조작, 처리 및 반응합니다. 따라서 이벤트 스트리밍은 데이터의 지속적인 흐름과 해석을 보장하여 올바른 정보가,
> 적절한 시간에 제공되도록 합니다.

## 이벤트 스트리밍은 무엇에 사용할수 있나요?(What can I use event streaming for?)

Event streaming is applied to a [wide variety of use cases](https://kafka.apache.org/powered-by) across a plethora of
industries and organizations. Its many examples include:

> 이벤트 스트리밍은 다양한 산업 및 조직에 걸쳐 다양한 사용 사례에 적용됩니다. 그 예는 다음과 같습니다.

- To process payments and financial transactions in real-time, such as in stock exchanges, banks, and insurances.
- To track and monitor cars, trucks, fleets, and shipments in real-time, such as in logistics and the automotive
  industry.
- To continuously capture and analyze sensor data from IoT devices or other equipment, such as in factories and wind
  parks.
- To collect and immediately react to customer interactions and orders, such as in retail, the hotel and travel
  industry, and mobile applications.
- To monitor patients in hospital care and predict changes in condition to ensure timely treatment in emergencies.
- To connect, store, and make available data produced by different divisions of a company.
- To serve as the foundation for data platforms, event-driven architectures, and microservices.

> - 주식 거래, 은행, 보험과 같이 실시간으로 금융 트랜잭션과 결제를 처리하기 위해서 사용됩니다.
> - 통계와 자동차 산업과 같이 실시간으로 자동차, 트럭, 함대, 선적 등을 모니터하고 추적하기 위해서 사용됩니다.
> - 공장 및 윈드파크와 같은 Iot 기기나 다른 장비로부터 센서 데이터를 지속적으로 캡처하고 분석하기 위해서 사용됩니다.
> - 소매업, 호텔, 여행업, 모바일 애플리케이션 등 고객의 상호작용과 주문을 수집하고 즉각적으로 대응합니다.
> - 응급 상황에서 적시에 치료할 수 있도록 병원 관리에서 환자를 모니터링하고 상태의 변화를 예측합니다.
> - 회사의 여러 부서에서 생상된 데이터를 연결, 저장 및 사용할 수 있도록 하는 것입니다.
> - 데이터 플랫폼, 이벤트 주도 아키텍처, 마이크로서비스를 위한 기반 역할을 수행합니다.

## 아파치 카프카는 이벤트 스트리밍 플랫폼입니다. 의미가 무엇인가?(Apache Kafka® is an event streaming platform. What does that mean?)

Kafka combines three key capabilities so you can implement [your use cases](https://kafka.apache.org/powered-by)
for event streaming end-to-end with a single battle-tested solution:

> 카프카는 다음과 같은 3가지 주요 기능을 결합하여 하나의 배틀 방식 솔루션으로 이벤트 스트리밍을 위한 사용 사례를 구현할 수 있습니다.

1. To publish (write) and subscribe to (read) streams of events, including continuous import/export of your data from
   other systems.
2. To store streams of events durably and reliably for as long as you want.
3. To process streams of events as they occur or retrospectively.

> 1. 다른 시스템으로부터 여러분들의 데이터의 지속적인 가져오기/내보내기를 포함하여 이벤트 스트림을 게시(쓰기)하고 구독(읽기)합니다.
> 2. 원하는 기간동안 이벤트 스트림을 지속적이고 안정적으로 저장합니다.
> 3. 이벤트가 발생할때 또는 소급하여 이벤트들의 스트림들을 처리합니다.

And all this functionality is provided in a distributed, highly scalable, elastic, fault-tolerant, and secure manner.
Kafka can be deployed on bare-metal hardware, virtual machines, and containers, and on-premises as well as in the cloud.
You can choose between self-managing your Kafka environments and using fully managed services offered by a variety of
vendors.

> 그리고 이 함수들의 모든 것이 높은 확장성, 탄력성, 장애 방지 및 안전한 방식으로 분산되어 제공됩니다.
> 카프카는 클라우드 뿐만 아니라 온프레미스, 컨테이너, 가상 머신, 베어 메탈 하드웨어(어떤 소프트웨어도 설치되어 있지 않은 상태)에 배포될 수 있습니다.
> 여러분들은 여러분들의 카프카 환경들 사이에서 선택할 수 있고 완전하게 관리되는 서비스들을 사용하는 것을 다양한 벤더들에 의해서 제공받을 수 있습니다.

## 카프카는 한마디로 어떻게 작동하나요?(How does Kafka work in a nutshell?)

Kafka is a distributed system consisting of servers and clients that communicate via a high-performance [TCP network
protocol](https://kafka.apache.org/protocol.html). It can be deployed on bare-metal hardware, virtual machines, and
containers in on-premise as well as cloud
environments.

> 카프카는 분산된 여러 서버들의 시스템 구성이고 높은 성능의 TCP 네트워크 프로토콜를 통해 소통하는 클라이언트들입니다.
> 카프카는 베어메탈 하드웨어, 가상 머신, 컨테이너, 클라우드 환경에 배포될 수 있습니다.

Servers: Kafka is run as a cluster of one or more servers that can span multiple datacenters or cloud regions. Some of
these servers form the storage layer, called the brokers. Other servers run Kafka Connect to continuously import and
export data as event streams to integrate Kafka with your existing systems such as relational databases as well as other
Kafka clusters. To let you implement mission-critical use cases, a Kafka cluster is highly scalable and fault-tolerant:
if any of its servers fails, the other servers will take over their work to ensure continuous operations without any
data loss.

> **서버**: 카프카는 여러개의 데이터 센터나 클라우드 리전을 돌리는 한개 이상의 서버들의 클러스터로서 수행합니다.
> 이 서버들의 몇몇은 브로커(brokers)라고 불리는 저장소 레이어로써 구성됩니다.
> 다른 서버들은 다른 카프카 클러스터뿐만 아니라 관계형 데이터베이스와 같은 여러분들의 이미 존재하는 시스템들과 같이 카프카를 통합시키기 위한 이벤트 스트림으로서
> 지속적으로 데이터를 가져오기/내보내기를 하기 위해서 카프카 연결을 수행합니다.
> 여러분들이 미션-크리티컬 사용 사례를 구현하기 위해서는 카프카 클러스는 매우 확장성있고 장애를 방지합니다.
> 만약 카프카 클러스터의 서버들이 몇몇 실패한다면 다른 서버들이 어떤 데이터 손실없이 지소적으로 연산을 확정하기 위해서 그들의 작업을 대신할 것입니다.

Clients: They allow you to write distributed applications and microservices that read, write, and process streams of
events in parallel, at scale, and in a fault-tolerant manner even in the case of network problems or machine failures.
Kafka ships with some such clients included, which are augmented by dozens of clients provided by the Kafka community:
clients are available for Java and Scala including the higher-level Kafka Streams library, for Go, Python, C/C++, and
many other programming languages as well as REST APIs.

> **클라이언트**: 네트워크 문제나 기계 장애가 발생한 경우에도 이벤트 스트리밍을 벼렬, 규모 및 내결함성 방식으로 읽고, 쓰고, 처리하는 분산 애플리케이션 및 마이크로 서비스를 작성할 수 있습니다.
> 카프카는 카프카 커뮤니티가 제공하는 수십개의 클라이언트에 의해서 증강된 일부 고객을 포함하여 배송합니다.:
> 클라이언트들은 노픈 레벨의 카프카 스트림 라이브러리를 포함하여 자바와 스칼라, Go, Python, C/C++ 및 REST API 뿐만 아니라 많은 프로그래밍 언어를 이용할 수 있습니다.

## 메인 컨셉과 기술(Main Concepts and Terminology)

An **event** records the fact that "something happened" in the world or in your business. It is also called record or
message in the documentation. When you read or write data to Kafka, you do this in the form of events. Conceptually, an
event has a key, value, timestamp, and optional metadata headers. Here's an example event:

> 이벤트(event)는 여러분들의 비즈니스나 세계에서 무언가 발생함하는 것에 대한 사실을 기록합니다.
> 이벤트는 또한 문서에서 레코드(record)나 메시지(message)로 불립니다.
> 여러분들이 데이터를 카프카에 읽거나 작성할때 여러분들은 이벤트의 폼에 읽거나 쓸수 있습니다. 개념적으로 이벤트는 키(key), 값(value), 타임스탬프(timestamp)
> 그리고 선태적으로 메타데이터 헤더를 가질수 있습니다.
> 이벤트 예시입니다.

- Event key: "Alice"
- Event value: "Made a payment of $200 to Bob"
- Event timestamp: "Jun. 25, 2020 at 2:06 p.m."

**Producers** are those client applications that publish (write) events to Kafka, and **consumers** are those that
subscribe
to (read and process) these events. In Kafka, producers and consumers are fully decoupled and agnostic of each other,
which is a key design element to achieve the high scalability that Kafka is known for. For example, producers never need
to wait for consumers. Kafka provides various guarantees such as the ability to process events exactly-once.

> 생산자(Producers)들은 카프카에 이벤트를 발행하는 클라이언트 애플리케이션들입니다.
> 그리고 소비자(Consumers)들은 이 이벤트들을 읽고 처리하기 위해 구독하는 것들입니다.
> 카프카에서 생산자와 소비자들은 완전하게 분리되어 있으며, 서로에 대해 불가지론적(인식불가능함, 즉 생산자와 소비자들은 서로를 인식하지 못함)입니다.
> 이것은 카프카가 알려진 높은 확장성을 달성하기 위한 핵심 설계 요소입니다. 예를 들어 생산자들은 절대로 소비자들을 대상으로 기다릴 필요가 없습니다.
> 카프카는 이벤트를 한번만 정확하게 처리할 수 있는 기능과 같은 다양한 보장을 제공합니다.

Events are organized and durably stored in topics. Very simplified, a topic is similar to a folder in a filesystem, and
the events are the files in that folder. An example topic name could be "payments". Topics in Kafka are always
multi-producer and multi-subscriber: a topic can have zero, one, or many producers that write events to it, as well as
zero, one, or many consumers that subscribe to these events. Events in a topic can be read as often as needed—unlike
traditional messaging systems, events are not deleted after consumption. Instead, you define for how long Kafka should
retain your events through a per-topic configuration setting, after which old events will be discarded. Kafka's
performance is effectively constant with respect to data size, so storing data for a long time is perfectly fine.

> 이벤트들은 항목(topic)에 구성되고 지속적으로 저장됩니다. 매우 간단하게 항목(topic)은 파일 시스템의 폴더와 비슷합니다. 그리고
> 이벤트들은 폴더안에 파일들입니다. 예를 들어 항목(topic) 이름이 "payments"가 될수 있습니다.
> 카프카 안에서 항목(topic)들은 언제나 멀티 생산자와 멀티 구독자입니다.: 항목(topic)에는 이벤트들을 작성하는 생산자가 0개 이상일 수 있고,
> 이러한 이벤트에 가입하는 소비자가 0개 이상일 수 있씁니다.

Topics are partitioned, meaning a topic is spread over a number of "buckets" located on different Kafka brokers. This
distributed placement of your data is very important for scalability because it allows client applications to both read
and write the data from/to many brokers at the same time. When a new event is published to a topic, it is actually
appended to one of the topic's partitions. Events with the same event key (e.g., a customer or vehicle ID) are written
to the same partition, and Kafka guarantees that any consumer of a given topic-partition will always read that
partition's events in exactly the same order as they were written.

> 항목들은 분할되어 있습니다. 즉, 항목은 서로 다른 여러 카프카 브로커들에 위치하는 여러 "버킷(buckets)"들에 분산되어 있습니다.
> 이렇게 분산된 여러분들의 데이터의 위치는 확장성을 위해서 매우 중요합니다. 왜냐하면 분산된 데이터들의 배치된 클라이언트 애플리케이션이
> 여러 브로커부터 동시에 읽고 쓰일수 있기 때문에 확장성에 매우 중요합니다.

![](https://kafka.apache.org/images/streams-and-tables-p1_p4.png)

Figure: This example topic has four partitions P1–P4. Two different producer clients are publishing, independently from
each other, new events to the topic by writing events over the network to the topic's partitions. Events with the same
key (denoted by their color in the figure) are written to the same partition. Note that both producers can write to the
same partition if appropriate.

> Figure: 이 예제 항목은 P1~P4까지 4개의 파티션을 가지고 있습니다. 서로 다른 두 생산자 클라이언트가 네트워크를 통해 항목의 파티션에 이벤트를
> 작성하여 항목에 대한 새로운 이벤트를 서로 독립적으로 퍼블리싱(발행)합니다.
> 그림에서 색상으로 표시된 키(key)가 동일한 이벤트는 동일한 파티션에 기록됩니다. 두 프로듀서 모두 적절한 경우 동일한 파티션에 기록할 수 있습니다.

To make your data fault-tolerant and highly-available, every topic can be replicated, even across geo-regions or
datacenters, so that there are always multiple brokers that have a copy of the data just in case things go wrong, you
want to do maintenance on the brokers, and so on. A common production setting is a replication factor of 3, i.e., there
will always be three copies of your data. This replication is performed at the level of topic-partitions.

> 여러분들의 데이터를 장애 방지와 높은 가용성으로 만들기 위해서는 모든 항목은 지리적 환경이나 데이터 센터를 막론하고 모든 주제를 복제할 수 있으므로,
> 문제가 발생한 경우를 대비하여 데이터 복사본을 가지고 있는 브로커가 항상 여러명 있으므로 브로커에 대한 유지보수 작업 등을 수행할 수 있습니다.

This primer should be sufficient for an introduction. The Design section of the documentation explains Kafka's various
concepts in full detail, if you are interested.

> 이 입문은 도입무에 충분해야 합니다. 문서의 디자인 섹션은 카프카의 다양한 개념에 대해서 설명합니다.

## Kafka APIs

In addition to command line tooling for management and administration tasks, Kafka has five core APIs for Java and
Scala:

> 관리 및 관리 작업을 위한 명령어 라인 도구 외에도 카프카는 자바와 스칼라를 위한 다섯 가지 API를 가지고 있습니다.

- The [Admin API](https://kafka.apache.org/documentation.html#adminapi) to manage and inspect topics, brokers, and other
  Kafka objects.
- The Producer API to publish (write) a stream of events to one or more Kafka topics.
- The Consumer API to subscribe to (read) one or more topics and to process the stream of events produced to them.
- The Kafka Streams API to implement stream processing applications and microservices. It provides higher-level
  functions to process event streams, including transformations, stateful operations like aggregations and joins,
  windowing, processing based on event-time, and more. Input is read from one or more topics in order to generate output
  to one or
  more topics, effectively transforming the input streams to output streams.
- The Kafka Connect API to build and run reusable data import/export connectors that consume (read) or produce (write)
  streams of events from and to external systems and applications so they can integrate with Kafka. For example, a
  connector to a relational database like PostgreSQL might capture every change to a set of tables. However, in
  practice, you typically don't need to implement your own connectors because the Kafka community already provides
  hundreds of
  ready-to-use connectors.

> - 항목, 브로커, 다른 카프카 객체들을 관리와 검사를 하기 위한 Admin API
> - 한개 이상의 카프카 항목들에 이벤트의 스트림을 퍼블리싱(작성)하기 위한 Producer API
> - 하나 이상의 항목에 대해 가입(읽기)하고 생성된 이벤트 스트림을 처리하기 위한 소비자 API 입니다.
> - 애플리케이션들과 마이크로서비들을 처리하는 스트림을 구현하기 위한 Kafka Streams API. Kafka Streams API는 변환, 집합체와 같은 상태 있는 연산들, 변환, 가입, 이벤트 타임을
    기반으로 처리하는 windowing을 포함한 이벤트 스트림을 처리하는 높은 레벨의 함수를 제공합니다.
> - 하나 이상의 항목에서 입력을 읽어 하나 이상의 항목으로 출력을 생성하여 입력 스트림을 출력 스트림으로 효과적으로 변환합니다.
> - Kafka Connect API는 외부 시스템 및 애플리케이션에서 이벤트 스트림을 소비(읽기)하거나 생성(쓰기)하는 재사용 가능한 데이터 가져오기/내보내기 커넥터를 구축하고 실행하여 Kafka와 통합할 수
    있습니다. 예를 들어 PostgreSQL와 같은 관게형 데이터베이스에 연결된 커넥터는 테이블들의 집합에 모든 변경을 캡처할 수 있습니다.
    그러나 여러분들은 현실적으로 여러들이 소유하는 커넥터들을 구현할 필요가 없습니다. 왜냐하면 카프카 커뮤니티는 이미 수백가지의 ready-to-use(사용할 준비가 되어있는) 커넥터를 제공합니다.

## 여기서 어디로 갈것인가(Where to go from here)

- To get hands-on experience with Kafka, follow the [Quickstart](https://kafka.apache.org/quickstart).
- To understand Kafka in more detail, read the [Documentation](https://kafka.apache.org/documentation/). You also have
  your choice of [Kafka books and academic papers](https://kafka.apache.org/books-and-papers).
- Browse through the [Use Cases](https://kafka.apache.org/powered-by) to learn how other users in our world-wide
  community are getting value out of Kafka.
- Join a [local Kafka meetup group](https://kafka.apache.org/events)
  and [watch talks from Kafka Summit](https://www.kafka-summit.org/past-events), the main conference of the Kafka
  community.

> - 카프카에 대한 경험을 가지기 위해서 Quickstart로 따라가세요.
> - 카프카에 대한 더 많은 디테일을 이해하기 위해서는 Documentation 문서를 읽으세요. 여러분들 또한 카프카 책과 아카데미를 선택할 수 있습니다.
> - Use Cases에서 검색하여 전세계 커뮤니티의 다른 사용자들이 카프카로부터 어떻게 가치를 얻고 있는지 알아보십시오.
> - 지역 카프카 미팅 그룹에 가입하고 카프카 서밋으로부터 대화를 보세요. 카프카 커뮤니티의 주요한 컨퍼런스입니다.

