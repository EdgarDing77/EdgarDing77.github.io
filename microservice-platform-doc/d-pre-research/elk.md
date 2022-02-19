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