# ELK

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

- filebeat：部署在每台应用服务器、数据库、中间件中，负责日志抓取与日志聚合
  - 日志聚合：把多行日志合并成一条，例如exception的堆栈信息等
- logstash：通过各种filter结构化日志信息，并把字段transform成对应的类型
- elasticsearch：负责存储和查询日志信息
- kibana：通过ui展示日志信息、还能生成饼图、柱状图等

## Elasticsearch

version: 7.15

### Introduction-Elasticsearch

随着近些年系统越来越大而复杂，系统间流转和产生大量的数据，现有许多技术都在集中如何解决数据仓库存储以及如何结构化这些数据，而数据若只是单纯的存储在磁盘上那便失去了其意义，如何去进行数据的分析和搜索便是Elasticsearch所做的事。

Elasticsearch提供近乎实时的**搜索**和**分析**对于**全类型数据**，当然不是每个问题都是搜索问题，Elasticsearch提供一个快速且灵活的数据处理：

- Add a search box to an app or website
- Store and analyze logs, metrics, and security event data
- Use machine learning to automatically model the behavior of your data in real time
- Automate business workflows using Elasticsearch as a storage engine
- Manage, integrate, and analyze spatial information using Elasticsearch as a geographic information system (GIS)
- Store and process genetic data using Elasticsearch as a bioinformatics research tool

#### Data in: documents and indices

ES不将数据存储为二维状，而采用存储为已被序列化成JSON的数据结构。当集群有多个ES节点时，存储的文档分布在整个集群中，并可从任何节点立即访问。

ES采用一种数据结构“inverted index”支持快速的full-text search，An inverted index会列出出现在任何文档中每个独特的单词，识别每个单词出现的文档。

索引可以认为是一个文档的优化集合，每个文档都是字段集合，且字段都是key-value键值对。默认情况下，ES 索引每个字段中的所有数据，每个索引字段都有一个专用的、优化的数据结构。同时我们可以自定义的进行动态映射去控制如何进行字段的存储和索引。

#### Information out: search and analyze

ES提供REST API进行集群管理、索引和数据搜索。在数据搜索上，其查询结构类似与SQL，可以构建SQL央视的查询在ES内部搜索和聚合本地数据。在数据分析上，ES聚合可以构建复杂的数据摘要并深入了解关键指标、模式和趋势。聚合与查询可以一起运行，可以在单个请求中搜索文档、过滤结果和可视化数据。

## Docker开发部署

> **注意：以下参照部署的版本偏低。**

### 环境要求

- Docker for Mac
- docker-compose

### 部署实践

**1、文件夹创建：**

分别用于存放Elasticsearch数据、Logstash插件和Logstash配置文件。

```bash
# pwd -> /Users/xxxx/
mkdir -p elk/elasticsearch/data
mkdir -p elk/elasticsearch/plugins
mkdir -p elk/logstash
mkdir -p elk/elasticsearch/conf
mkdir -p elk/docker # 存放docker-compose.yml文件
```

**2、准备和修改配置文件**

- 运行和下载ELK

```bash
docker run -p 5601:5601 -p 9200:9200 -p 9300:9300 -p 5044:5044 --name elk -d sebp/elk:651
```

- 准备elasticsearch配置文件

```bash
#复制elasticsearch的配置出来
docker cp elk:/etc/elasticsearch/elasticsearch.yml elk/elasticsearch/conf
```

- 修改elasticsearch.yml配置

```bash
# 修改cluster.name
cluster.name: my-es
# 最后新增三个参数
thread_pool.bulk.queue_size: 1000
http.cors.enabled: true
http.cors.allow-origin: "*"
```

- 准备logstash配置文件

```bash
#复制logstash的配置出来
docker cp elk:/etc/logstash/conf.d/. elk/logstash/conf/
```

- 准备logstash的patterns文件

```bash
# 新建一个patterns文件夹
mkdir -p elk/logstash/patterns
# 新建一个java的patterns文件，vim java.patterns 内容如下
# user-center
MYAPPNAME ([0-9a-zA-Z_-]*)
# RMI TCP Connection(2)-127.0.0.1
MYTHREADNAME ([0-9a-zA-Z._-]|\(|\)|\s)*
```

- 删除02-beats-input.conf最后三句，去掉强制认证

```bash
vim elk/logstash/conf/02-beats-input.conf
#ssl => true 
#ssl_certificate => "/pki/tls/certs/logstash.crt"
#ssl_key => "/pki/tls/private/logstash.key"
```

- 修改10-syslog.conf配置

>  **注意，下面的logstash结构化配置样例都是以本工程的日志格式配置，并不是通用的**

```bash
filter {
  if [type] == "syslog" {
    grok {
      match => { "message" => "%{SYSLOGTIMESTAMP:syslog_timestamp} %{SYSLOGHOST:syslog_hostname} %{DATA:syslog_program}(?:\[%{POSINT:syslog_pid}\])?: %{GREEDYDATA:syslog_message}" }
      add_field => [ "received_at", "%{@timestamp}" ]
      add_field => [ "received_from", "%{host}" ]
    }
    syslog_pri { }
    date {
      match => [ "syslog_timestamp", "MMM  d HH:mm:ss", "MMM dd HH:mm:ss" ]
    }
  }
  if [fields][docType] == "sys-log" {
    grok {
      patterns_dir => ["/opt/elk/logstash/patterns"]
      match => { "message" => "\[%{NOTSPACE:appName}:%{NOTSPACE:serverIp}:%{NOTSPACE:serverPort}\] %{TIMESTAMP_ISO8601:logTime} %{LOGLEVEL:logLevel} %{WORD:pid} \[%{MYAPPNAME:traceId}\] \[%{MYTHREADNAME:threadName}\] %{NOTSPACE:classname} %{GREEDYDATA:message}" }
      overwrite => ["message"]
    }
    date {
      match => ["logTime","yyyy-MM-dd HH:mm:ss.SSS Z"]
    }
    date {
      match => ["logTime","yyyy-MM-dd HH:mm:ss.SSS"]
      target => "timestamp"
      locale => "en"
      timezone => "+08:00"
    }
    mutate {  
      remove_field => "logTime"
      remove_field => "@version"
      remove_field => "host"
      remove_field => "offset"
    }
  }
  if [fields][docType] == "point-log" {
    grok {
      patterns_dir => ["/opt/elk/logstash/patterns"]
      match => { 
        "message" => "%{TIMESTAMP_ISO8601:logTime}\|%{MYAPPNAME:appName}\|%{WORD:resouceid}\|%{MYAPPNAME:type}\|%{GREEDYDATA:object}"
      }
    }
    kv {
        source => "object"
        field_split => "&"
        value_split => "="
    }
    date {
      match => ["logTime","yyyy-MM-dd HH:mm:ss.SSS Z"]
    }
    date {
      match => ["logTime","yyyy-MM-dd HH:mm:ss.SSS"]
      target => "timestamp"
      locale => "en"
      timezone => "+08:00"
    }
    mutate {
      remove_field => "logTime"
      remove_field => "@version"
      remove_field => "host"
      remove_field => "offset"
    }
  }
}
```

- 修改30-output.conf配置

```bash
output {
  if [fields][docType] == "sys-log" {
    elasticsearch {
      hosts => ["localhost"]
      manage_template => false
      index => "sys-log-%{+YYYY.MM.dd}"
      document_type => "%{[@metadata][type]}"
    }
  }
  if [fields][docType] == "point-log" {
    elasticsearch {
      hosts => ["localhost"]
      manage_template => false
      index => "point-log-%{+YYYY.MM.dd}"
      document_type => "%{[@metadata][type]}"
      routing => "%{type}"
    }
  }
}
```

**3、docker-compose.yml文件(位置：elk/docker/docker-compose.yml)**

使用运行脚本或者docker-compose

```sh
docker stop elk
docker rm elk

docker run -p 5601:5601 -p 9200:9200 -p 9300:9300 -p 5044:5044 \
    -e LS_HEAP_SIZE="1g" -e ES_JAVA_OPTS="-Xms2g -Xmx2g" \
    -v $PWD/elasticsearch/data:/var/lib/elasticsearch \
    -v $PWD/elasticsearch/plugins:/opt/elasticsearch/plugins \
    -v $PWD/logstash/conf:/etc/logstash/conf.d \
    -v $PWD/logstash/patterns:/opt/logstash/patterns \
    -v $PWD/elasticsearch/conf/elasticsearch.yml:/etc/elasticsearch/elasticsearch.yml \
    -v $PWD/elasticsearch/log:/var/log/elasticsearch \
    -v $PWD/logstash/log:/var/log/logstash \
    --name elk \
    -d sebp/elk:651
```



## reference

- 官网：https://www.elastic.co/cn/what-is/elk-stack
- Elasticsearch：https://www.elastic.co/guide/en/elasticsearch/reference/current/index.html
- Elasticsearch2.X：https://github.com/elastic/elasticsearch-definitive-guide/
- [docker部署在MAC参考](https://blog.csdn.net/kouxinsu8594/article/details/107129482)