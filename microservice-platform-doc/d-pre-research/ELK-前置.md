# ELK-前置

## Introduction

> 「What」ELK是三个开源项目的字母缩写，这三个项目分别是Elasticsearch、Logstash和Kibana。

- Elasticsearch：一个基于JSON的搜索和分析引擎。
- Logstash：服务器端数据处理管道。Spring Boot应用整合来Logstash以后会把日志发送给Logstash，Logstash再把日志转发给Elasticsearch。
- Kibana：在Elasticsearch中进行数据可视化。

**ELK Stack：**指Elastic Stack，本质是Elasticsearch + Kibana组成。

## Overview

![Image](https://cdn.jsdelivr.net/gh/edgarding77/microservice-platform-doc@latest/image/tech/elk-overview.png)

Logstash和Beats进行数据收集、聚合和存储进Elasticsearch，Kibana允许我们对数据进行交互式探索、可视化和监控，Elasticsearch是进行索引、搜索和分析的地方。

组件分工：

- 11filebeat：部署在每台应用服务器、数据库、中间件中，负责日志抓取与日志聚合
  - 日志聚合：把多行日志合并成一条，例如exception的堆栈信息等
- logstash：通过各种filter结构化日志信息，并把字段transform成对应的类型
- elasticsearch：负责存储和查询日志信息
- kibana：通过ui展示日志信息、还能生成饼图、柱状图等

### 使用说明

- 5601:Kibana web interface
- 9200:Elasticsearch JSON interface
- 9300:Elasticsearch transport interface
- 5044:Logstash Beats interface

- 9600:Logstash API endpoint

结构化日志：

```json
{
  "timestamp": "时间",
  "message": "具体日志信息",
  "threadName": "线程名",
  "serverPort": "服务端口",
  "serverIp": "服务ip",
  "logLevel": "日志级别",
  "appName": "工程名称",
  "classname": "类名"
}
```

- 5601 (Kibana web interface).
- 9200 (Elasticsearch JSON interface).
- 5044 (Logstash Beats interface, receives logs from Beats such as Filebeat – see the *[Forwarding logs with Filebeat](https://elk-docker.readthedocs.io/#forwarding-logs-filebeat)* section).

## ES与SpringBoot版本对应表

官网信息：https://docs.spring.io/spring-data/elasticsearch/docs/current/reference/html/#preface.versions

The following table shows the Elasticsearch versions that are used by Spring Data release trains and version of Spring Data Elasticsearch included in that, as well as the Spring Boot versions referring to that particular Spring Data release train:

|                  Spring Data Release Train                   |                  Spring Data Elasticsearch                   | Elasticsearch |                       Spring Framework                       |                         Spring Boot                          |
| :----------------------------------------------------------: | :----------------------------------------------------------: | :-----------: | :----------------------------------------------------------: | :----------------------------------------------------------: |
| 2021.1 (Q)[[1](https://docs.spring.io/spring-data/elasticsearch/docs/current/reference/html/#_footnotedef_1)] | 4.3.x[[1](https://docs.spring.io/spring-data/elasticsearch/docs/current/reference/html/#_footnotedef_1)] |    7.15.2     | 5.3.x[[1](https://docs.spring.io/spring-data/elasticsearch/docs/current/reference/html/#_footnotedef_1)] | 2.5 .x[[1](https://docs.spring.io/spring-data/elasticsearch/docs/current/reference/html/#_footnotedef_1)] |
|                       2021.0 (Pascal)                        |                            4.2.x                             |    7.12.0     |                            5.3.x                             |                            2.5.x                             |
|                       2020.0 (Ockham)                        |                            4.1.x                             |     7.9.3     |                            5.3.2                             |                            2.4.x                             |
|                           Neumann                            |                            4.0.x                             |     7.6.2     |                            5.2.12                            |                            2.3.x                             |
|                            Moore                             |                            3.2.x                             |    6.8.12     |                            5.2.12                            |                            2.2.x                             |
| Lovelace[[2](https://docs.spring.io/spring-data/elasticsearch/docs/current/reference/html/#_footnotedef_2)] | 3.1.x[[2](https://docs.spring.io/spring-data/elasticsearch/docs/current/reference/html/#_footnotedef_2)] |     6.2.2     |                            5.1.19                            |                            2.1.x                             |
| Kay[[2](https://docs.spring.io/spring-data/elasticsearch/docs/current/reference/html/#_footnotedef_2)] | 3.0.x[[2](https://docs.spring.io/spring-data/elasticsearch/docs/current/reference/html/#_footnotedef_2)] |     5.5.0     |                            5.0.13                            |                            2.0.x                             |
| Ingalls[[2](https://docs.spring.io/spring-data/elasticsearch/docs/current/reference/html/#_footnotedef_2)] | 2.1.x[[2](https://docs.spring.io/spring-data/elasticsearch/docs/current/reference/html/#_footnotedef_2)] |     2.4.0     |                            4.3.25                            |                            1.5.x                             |

Support for upcoming versions of Elasticsearch is being tracked and general compatibility should be given assuming the usage of the [high-level REST client](https://docs.spring.io/spring-data/elasticsearch/docs/current/reference/html/#elasticsearch.clients.rest).

**因为SpringData版本为4.2.7，因此应选用ES7.12.0。**

## ES相关组件版本对应表

官网信息：https://www.elastic.co/cn/support/matrix#matrix_compatibility

**与 Elasticsearch（5.x、6.x、7.x）的兼容性**

| Elasticsearch | Kibana | X-Pack |   Beats^*    | Elastic Agent^* |   Logstash^*   | ES-Hadoop (jar) |   APM Server    | App Search | Enterprise Search | Elastic Endgame |
| :-----------: | :----: | :----: | :----------: | :-------------: | :------------: | :-------------: | :-------------: | :--------: | :---------------: | :-------------: |
|     6.5.x     | 6.5.x  | N/A**  | 5.6.x-6.8.x  |                 |  5.6.x-6.8.x   |   6.0.x-6.8.x   |   6.2.x-6.8.x   |            |                   |                 |
|     6.6.x     | 6.6.x  | N/A**  | 5.6.x-6.8.x  |                 |  5.6.x-6.8.x   |   6.0.x-6.8.x   |   6.2.x-6.8.x   |            |                   |                 |
|     6.7.x     | 6.7.x  | N/A**  | 5.6.x-6.8.x  |                 |  5.6.x-6.8.x   |   6.0.x-6.8.x   |   6.2.x-6.8.x   |            |                   |                 |
|     6.8.x     | 6.8.x  | N/A**  | 5.6.x-6.8.x  |                 |  5.6.x-6.8.x   |   6.0.x-6.8.x   |   6.2.x-6.8.x   |            |                   |                 |
|     7.0.x     | 7.0.x  | N/A**  | 6.8.x-7.17.x |                 |  6.8.x-7.17.x  |  6.8.x-7.16.x   | 7.0.x-7.17.x*** |            |                   | 3.14.x - 3.18.x |
|     7.1.x     | 7.1.x  | N/A**  | 6.8.x-7.17.x |                 |  6.8.x-7.17.x  |  6.8.x-7.16.x   | 7.0.x-7.17.x*** |            |                   | 3.14.x - 3.18.x |
|     7.2.x     | 7.2.x  | N/A**  | 6.8.x-7.17.x |                 |  6.8.x-7.17.x  |  6.8.x-7.16.x   | 7.0.x-7.17.x*** |   7.2.x    |                   | 3.14.x - 3.18.x |
|     7.3.x     | 7.3.x  | N/A**  | 6.8.x-7.17.x |                 |  6.8.x-7.17.x  |  6.8.x-7.16.x   | 7.0.x-7.17.x*** |   7.3.x    |                   | 3.14.x - 3.18.x |
|     7.4.x     | 7.4.x  | N/A**  | 6.8.x-7.17.x |                 |  6.8.x-7.17.x  |  6.8.x-7.16.x   | 7.0.x-7.17.x*** |   7.4.x    |                   | 3.14.x - 3.18.x |
|     7.5.x     | 7.5.x  | N/A**  | 6.8.x-7.17.x |                 |  6.8.x-7.17.x  |  6.8.x-7.16.x   | 7.0.x-7.17.x*** |   7.5.x    |                   | 3.14.x - 3.18.x |
|     7.6.x     | 7.6.x  | N/A**  | 6.8.x-7.17.x |                 |  6.8.x-7.17.x  |  6.8.x-7.16.x   | 7.0.x-7.17.x*** |   7.6.x    |                   | 3.14.x - 3.18.x |
|     7.7.x     | 7.7.x  | N/A**  | 6.8.x-7.17.x |                 |  6.8.x-7.17.x  |  6.8.x-7.16.x   | 7.0.x-7.17.x*** |  N/A****   |       7.7.x       | 3.14.x - 3.18.x |
|     7.8.x     | 7.8.x  | N/A**  | 6.8.x-7.17.x |      7.8.x      |  6.8.x-7.17.x  |  6.8.x-7.16.x   | 7.0.x-7.17.x*** |  N/A****   |       7.8.x       | 3.14.x - 3.18.x |
|     7.9.x     | 7.9.x  | N/A**  | 6.8.x-7.17.x |      7.9.x      |  6.8.x-7.17.x  |  6.8.x-7.16.x   | 7.0.x-7.17.x*** |  N/A****   |       7.9.x       | 3.14.x - 3.19.x |
|    7.10.x     | 7.10.x | N/A**  | 6.8.x-7.17.x |     7.10.x      |  6.8.x-7.17.x  |  6.8.x-7.16.x   | 7.0.x-7.17.x*** |  N/A****   |      7.10.x       | 3.14.x - 3.20.x |
|    7.11.x     | 7.11.x | N/A**  | 6.8.x-7.17.x |     7.11.x      |  6.8.x-7.17.x  |  6.8.x-7.16.x   | 7.0.x-7.17.x*** |  N/A****   |      7.11.x       | 3.14.x - 3.21.x |
|    7.12.x     | 7.12.x | N/A**  | 6.8.x-7.17.x |     7.12.x      |  6.8.x-7.17.x  |  6.8.x-7.16.x   | 7.0.x-7.17.x*** |  N/A****   |      7.12.x       | 3.14.x - 3.22.x |
|    7.13.x     | 7.13.x | N/A**  | 6.8.x-7.17.x |     7.13.x      |  6.8.x-7.17.x  |  6.8.x-7.16.x   | 7.0.x-7.17.x*** |  N/A****   |      7.13.x       | 3.14.x - 3.23.x |
|    7.14.x     | 7.14.x | N/A**  | 6.8.x-7.17.x |   7.14.x*****   |  6.8.x-7.17.x  |  6.8.x-7.16.x   | 7.0.x-7.17.x*** |  N/A****   |      7.14.x       | 3.14.x - 3.24.x |
|    7.15.x     | 7.15.x | N/A**  | 6.8.x-7.17.x | 7.14.x - 7.15.x |  6.8.x-7.17.x  |  6.8.x-7.16.x   | 7.0.x-7.17.x*** |  N/A****   |      7.15.x       | 3.14.x - 3.24.x |
|    7.16.x     | 7.16.x | N/A**  | 6.8.x-7.17.x | 7.14.x - 7.16.x |  6.8.x-7.17.x  |                 | 7.0.x-7.17.x*** |  N/A****   |      7.16.x       | 3.14.x - 3.24.x |
|    7.17.x     | 7.17.x | N/A**  | 6.8.x-7.17.x | 7.14.x - 7.17.x |  6.8.x-7.17.x  |                 | 7.0.x-7.17.x*** |  N/A****   |      7.17.x       | 3.14.x - 3.25.x |
|     8.0.x     | 8.0.x  | N/A**  | 7.17.x-8.0.x | 7.17.x - 8.0.x  | 7.17.x - 8.0.x |                 |  7.17.x-8.0.x   |            |   7.17.x-8.0.x    |                 |



Elasticsearch 输出兼容性 — Beats、Logstash 和 Elastic 代理将数据索引到 Elasticsearch；**从 6.3 版开始，Elastic Stack 的默认分发版中将包含 X-Pack 功能。请查看 https://www.elastic.co/what-is/open-x-pack。**APM Server 6.7 和 6.8 版可与 Elasticsearch 7.0 版一起使用，但数据必须使用升级助手进行迁移才能在 Kibana 7.0 版中可见。App Search 从 7.7.0 版开始将移至企业搜索中。

## reference

- 官网：https://www.elastic.co/cn/what-is/elk-stack
- Elasticsearch：https://www.elastic.co/guide/en/elasticsearch/reference/current/index.html
- Elasticsearch2.X：https://github.com/elastic/elasticsearch-definitive-guide/
- [docker部署在MAC参考](https://blog.csdn.net/kouxinsu8594/article/details/107129482)