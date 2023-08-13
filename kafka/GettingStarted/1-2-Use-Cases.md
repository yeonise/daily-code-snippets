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
