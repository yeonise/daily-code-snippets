# 1.2 Use Cases

Here is a description of a few of the popular use cases for Apache Kafka®. For an overview of a number of these areas in
action,
see [this blog post](https://engineering.linkedin.com/distributed-systems/log-what-every-software-engineer-should-know-about-real-time-datas-unifying)

## Messaging

Kafka works well as a replacement for a more traditional message broker. Message brokers are used for a variety of
reasons (to decouple processing from data producers, to buffer unprocessed messages, etc). In comparison to most
messaging systems Kafka has better throughput, built-in partitioning, replication, and fault-tolerance which makes it a
good solution for large scale message processing applications.

> 카프카는 더 전통적인 메시지 브로커의 대체제로써 잘 작동합니다.
> 메시지 브로커들은 다양한 이유들로 사용됩니다.(데이터 생산자로부터 처리 연결 해제, 처리되지 않은 메시지 버퍼링 등).
> 대부분의 메시징 시스템과 비교하여 **카프카**는 처리량, 내장 파티션, 복제 및 내결함성이 향상되어 **대규모 메시지 처리 애플리케이션에 적합한 솔루션**입니다.

In our experience messaging uses are often comparatively low-throughput, but may require low end-to-end latency and
often depend on the strong durability guarantees Kafka provides.

> 우리의 경험으로는 메시지 사용이 비교적 낮은 처리량인 경우가 많습니다. 그러나 잛은 종단 간 지연시간이 요구될 수 있으며, 종종 카프카는 제공하는 강력한 내구성 보장에 의존합니다.

In this domain Kafka is comparable to traditional messaging systems such as [ActiveMQ](https://activemq.apache.org/)
or [RabbitMQ](https://www.rabbitmq.com/).

> 이 도메인에서 카프카는 ActiveMQ나 RebbitMQ로서 전통적인 메시지 시스템들과 비교가 가능합니다.

## 웹 사이트 활동 추적(Website Activity Tracking)

The original use case for Kafka was to be able to rebuild a user activity tracking pipeline as a set of real-time
publish-subscribe feeds. This means site activity (page views, searches, or other actions users may take) is published
to central topics with one topic per activity type. These feeds are available for subscription for a range of use cases
including real-time processing, real-time monitoring, and loading into Hadoop or offline data warehousing systems for
offline processing and reporting.

> 카프카에 대한 오리지널 유즈케이스는 실시간 발행-구독 피드로서 사용자 활동을 추적하는 파이프라인을 재구축할 수 있는 것이었습니다.
> 이것은 사이트 활동(페이지 보기, 검색, 사용자가 할수 있는 다른 활동)이 활동 유형당 하나의 주제를 가지는 중심 주제에 발행됨을 의미합니다.

Activity tracking is often very high volume as many activity messages are generated for each user page view.

> 활동 추적은 각각의 사용자 페이지 보기에 대하여 생성되는 많은 활동 메시지들이기 때문에 종종 매우 높은 볼륨입니다.

## Metrics

Kafka is often used for operational monitoring data. This involves aggregating statistics from distributed applications
to produce centralized feeds of operational data.

> 카프카는 종종 데이터를 모니터링하는 연산에 사용됩니다. 이 데이터는 연산하는 데이터의 중앙화된 피드들을 생산하는 분산된 애플리케이션들로부터 통계 집계와 관련되어 있습니다.

## 로그 집계(Log Aggregation)

Many people use Kafka as a replacement for a log aggregation solution. Log aggregation typically collects physical log
files off servers and puts them in a central place (a file server or HDFS perhaps) for processing. Kafka abstracts away
the details of files and gives a cleaner abstraction of log or event data as a stream of messages. This allows for
lower-latency processing and easier support for multiple data sources and distributed data consumption. In comparison to
log-centric systems like Scribe or Flume, Kafka offers equally good performance, stronger durability guarantees due to
replication, and much lower end-to-end latency.

> 많은 사람들이 카프카를 로그 집계 솔루션에 대한 대체제로써 사용합니다. 로그 집계는 일반적으로 물리적 로그 파일을 수집하여 중앙 장소(파일 서버 또는 HDFS)에 저장하여 처리합니다.
> 카프카는 파일의 세부 정보를 추상화하고 로그 또는 이벤트 데이터를 메시지 스트림으로 더 깨끗하게 추상화합니다.
> 이를 통해 지연시간을 줄이고 여러 데이터 소스 및 분산 데이터 소비를 더 쉽게 지원할 수 있습니다.
> Scribe나 Flume과 같은 로그-중앙 시스템과 비교하여 카프카는 좋은 성능, 복제로 인해 더욱 강력한 내구성 보장 및 훨신 종단간 낮은 지연시간을 제공합니다.

## 스트림 프로세싱(Stream Processing)

Many users of Kafka process data in processing pipelines consisting of multiple stages, where raw input data is consumed
from Kafka topics and then aggregated, enriched, or otherwise transformed into new topics for further consumption or
follow-up processing. For example, a processing pipeline for recommending news articles might crawl article content from
RSS feeds and publish it to an "articles" topic; further processing might normalize or deduplicate this content and
publish the cleansed article content to a new topic; a final processing stage might attempt to recommend this content to
users. Such processing pipelines create graphs of real-time data flows based on the individual topics. Starting in
0.10.0.0, a light-weight but powerful stream processing library called Kafka Streams is available in Apache Kafka to
perform such data processing as described above. Apart from Kafka Streams, alternative open source stream processing
tools include Apache Storm and Apache Samza.

> 카프카의 많은 사용자들이 카프카 항목으로부터 소비된 날것의 입력 데이터가 소비되고 집계, 농축, 추가 소비 또는 후속처리 되었을때 여러 단계들로 구성되는 프로세싱 파이프라인에서 데이터를 처리합니다.
> 예를 들어 뉴스 기사를 추천하기 위한 프로세싱 파이프라인은 RSS 피드에서 기사 내용을 기어 나와 "기사"주제로 발행할 수 있습니다.
> 추가 처리는 이 내용을 정규화 또는 중복제거하고 정리된 기사 내용을 새 주제에 발행 할 수 있습니다.
> 마지막 프로세싱 단계는 이 기사 내용을 사용자들에게 추천하는 것을 시도할 수 있습니다.
> 이러한 프로세싱 파이프라인들은 개인적인 항목을 기반으로 실시간 데이터 흐름의 그래프를 생성합니다.
> 0.10.0.0부터 Apache Kafka에서 위와 같은 데이터 처리를 수행할 수 있도록 경량이지만 강력한 Kafka Stream이라는 스트림 처리 라이브러리를 사용할 수 있습니다.

## 이벤트 소스(Event Sourcing)

[Event sourcing](http://martinfowler.com/eaaDev/EventSourcing.html) is a style of application design where state changes
are logged as a time-ordered sequence of records. Kafka's support for very large stored log data makes it an excellent
backend for an application built in this style.

> 이벤트 소스(Event sourcing)은 상태 변겨들이 시간순으로 로깅되었을때 애플리케이션 설계의 스타일입니다.
> 카프카는 대용량 저장 로그 데이터를 지원하기 때문에 이러한 스타일로 구축된 애플리케이션에 적합한 백엔드입니다.

## 커밋 로그(Commit Log)

Kafka can serve as a kind of external commit-log for a distributed system. The log helps replicate data between nodes
and acts as a re-syncing mechanism for failed nodes to restore their data. The log compaction feature in Kafka helps
support this usage. In this usage Kafka is similar to Apache BookKeeper project.

> 카프카는 분산 시스템을 대상으로 외부 커밋 로그의 종류로서 서빙할 수 있습니다.
> 로그는 노드간 데이터를 복제하는데 도움이 되며 실패한 노드가 데이터를 복원하는데 재동기화 매커니즘 역할을 합니다.
> 카프카에 로그 압축 기능은 이 사용량에 도움을 줍니다.
> 이 사용법에서 카프카는 Apache BookKeeper 프로젝트와 유사합니다.




