---
layout:     post
title:      logback+ELK配置
subtitle:   elk+logback快速搭建流程
date:       2018-04-28
author:     lc
header-img: img/post-bg-universe.jpg
catalog:    true
tags:
    - elasticSearch
---


# logback+ELK
项目中需要获取用户访问应用的日志做数据分析，目前处理日志的工具中，ELK算比较成熟的了，其具体介绍如下：
- **Logstash** : logstash算是ELK中的一个中转站，它可以通过**主动扫描**和**被动获取**两种方式来获取日志。
其具体实现为：**扫描文件**（主动扫描）、**建立tcp连接接收发送过来的信息**（被动获取）。获取到日志后，logstash可以对日志内容进行过滤，最后写入ES中。
- **Elasticsearch** : you kown, for search!
- **Kibana**:kibana是一个可视化工具，专用于对ES进行可视化分析。

## 安装及配置
*安装ELK之前需要安装JDK*
#### Elasticsearch
- [下载es](https://www.elastic.co/downloads/elasticsearch),解压，es的config文件夹里有两个配置文件需要修改:
```
//设置jvm.option
vi jvm. options
//分配虚拟机内存，比如这里设的是2g，在es启动的时候就会去初始化2g的内存，如果使用的是购买的云主机，可能总共内存才2g，这就会导致es启动报错，或者别的应用没有内存空间来启动，所以这个值尽量设置的小一点。
-Xms2g
-Xmx2g
//设置elasticsearch.yml
vi Elasticsearch.yml
network.host:12.12.12.12 //设置成安装es机器的ip地址
http.port:9200
discovery.zen.ping.unicast.hosts:["ip地址1","ip地址2",...]//集群ip地址
```
- 启动es
```
bin/elasticsearch
```
- 访问es，http://12.12.12.12:9200

#### logstash：
- [下载logstash](https://www.elastic.co/downloads/logstash)，解压
- 创建配置文件供logstash启动时加载，如test.conf,input有多种配置，如tcp、file等，filter也有gork、csv等，output除了es也有别的输出目的地。
```
//输入配置
input {
    tcp {
      host => "12.221.21.22"//安装logstash的机器的ip地址
      port => 9250//端口号
      mode => "server"
      tags => ["tags"]
      codec => "json"//按json格式编码
    }
}
//过滤配置
filter {
    csv{
      source => "message"
      columns => ["column a","column b"] //从message中取出字段名
      remove_field => ["column c","column b"] //去除不需要的字段名
    }
}
//输出配置
output {
    stdout{codec =>rubydebug}
    elasticsearch {
      hosts => ["http://12.221.21.222:9200"] //安装es集群的地址
      flush_size => 1000 //刷新频率
      index => "test-logs" //es中创建索引的名称
    }
}
```
- 示例：对上面的配置，**若输入日志为123,234,456，logstash以逗号为分隔符，自动分割日志为value值，输出为，column a:123,column b:234**
- 启动logstash：
```
bin/logstash -f test.conf --debug(debug模式)，
bin/logstash -f test.conf &(后台模式)
```

#### Kibana
- [下载Kibana](https://www.elastic.co/downloads/kibana),解压
- 修改配置文件kibana.yml
```
server.host:"0.0.0.0"
elasticsearch.url:"http://12.12.12.12:9200" //es的访问连接
```
- 启动kibana
```
bin/kibana
```
- 访问kibana，浏览器输入：http://localhost:5601，就可以对绑定的es集群进行各种操作了

#### logback
使用logback将应用里的日志输出到logstash，输出到logstash中的日志必须是指定格式的特定日志信息，所以，需要**过滤**和**格式化**

```
<dependency>
    <groupId>net.logstash.logback</groupId>
    <artifactId>logstash-logback-encoder</artifactId>
    <version>4.11</version>
</dependency>
```


- logback.xml 文件配置(添加appender)
```
<appender name="stash" class="net.logstash.logback.appender.LogstashTcpSocketAppender">
    <destination>12.12.12.12:9250</destination>//安装logstash机器的ip地址
    <filter class="com.program.interceptor.ELKFilter"/>//引入过滤类

  <!-- encoder is required -->
    <encoder charset="UTF-8" class="net.logstash.logback.encoder.LogstashEncoder" />
</appender>
```
还要将新添加的appender添加到<root>中
```
<root level="DEBUG">
    <appender-ref ref="stash" />
</root>

```
- 过滤类编写
```
public class ELKFilter extends Filter<ILoggingEvent> {
    @Override
    public FilterReply decide(ILoggingEvent event) {
        if (event.getMessage().startsWith("KeyWords")) {//只有以"KeyWords"开头的日志被输出到logstash
            return FilterReply.ACCEPT;
        } else {
            return FilterReply.DENY;
        }
    }
}
```

- 确定日志格式后输出
