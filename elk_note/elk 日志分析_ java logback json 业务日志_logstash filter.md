# elk 日志分析_ java logback json 业务日志_logstash filter
* json 格式可将单条日志的多行输出合并，防止在elk接收过程中单条日志被拆分成多行传输及显示，影响日志查询体验；
* json 格式标准化以便 elk 对日志内容进行字段拆分分析，方便做聚类统计及敏感字段过滤；
* 字段拆分后，按照字段内容搜索可提高 elasticsearch 查询速率，以避免全文查询。

## java logback json 日志配置
### logback 配置
* 业务日志以 json 格式输出，方便 logstash 拆分解析
* 使用 utf8 编码，防止中文被输出为十六进程编码
* 日志每 100MB 进行自动切分，防止单个日志过大导致日志原文文本文件难以打开及备份
* 日志名按照 gitlab 上标示的工程名为准，但必须以 *.log 格式命名，所有实时日志必须一直输出在该日志中，防止elk收集日志使用正则匹配文件名会导致性能损耗，日志实时收集延时
```
<?xml version="1.0" encoding="UTF-8"?>
<configuration scan="true" scanPeriod="10 seconds">

    <!-- 对应pattern的msg -->
    <conversionRule conversionWord="message" converterClass="com.companyname.common.log.filter.CustomConverter"/>

    <property name="log_path" value="/sdata/var/log"/>
    <property name="service_name" value="companyname-message-service"/>

    <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
        <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
            <pattern>%d{yyyy-MM-dd HH:mm:ss} [%thread] %-5level %logger{60} - %message%n</pattern>
            <!--<charset>UTF-8</charset>-->
        </encoder>
    </appender>

    <appender name="FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <file>${log_path}/${service_name}/${service_name}.log</file>
        <rollingPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedRollingPolicy">
            <FileNamePattern>${log_path}/${service_name}/${service_name}.%d{yyyy-MM-dd}-%i.log</FileNamePattern>
            <maxFileSize>100MB</maxFileSize>
            <maxHistory>365</maxHistory>
            <totalSizeCap>50GB</totalSizeCap>
        </rollingPolicy>
        <encoder charset="UTF-8" class="net.logstash.logback.encoder.LoggingEventCompositeJsonEncoder">
            <jsonFactoryDecorator class="com.companyname.common.util.DefaultJsonFactoryDecorator"/>
            <providers>
                <pattern>
                    <pattern>
                        {
                        "timestamp":"%d{yyyy-MM-dd HH:mm:ss.SSS}",
                        "level": "%level",
                        "thread": "%thread",
                        "class": "%logger{60}",
                        "message": "%message",
                        "service_name": "${service_name}",
                        "stack_trace": "%exception{10}"
                        }
                    </pattern>
                </pattern>
            </providers>
        </encoder>
    </appender>

    <root level="info">
        <appender-ref ref="FILE"/>
    </root>
</configuration>
```
### 日志级别
#### inteceptor
1. ip记录 - INFO</br>
#### api
1. 入参校验失败结果记录 - WARN</br>
2. 入参校验成功的参数内容记录 - INFO</br>
3. 没有入参的成功进入controller记录 - INFO</br>
#### service
1. 捕获异常日志记录 - ERROR</br>
2. 业务逻辑校验不通过 - WARN</br>
3. 参数校验为空，走默认策略且需记录的情况 - WARN</br>
4. 信息记录 - INFO</br>
#### component
1. 捕获异常日志记录 - ERROR</br>
2. 入参或业务逻辑校验不通过 - WARN</br>
3. 参数校验为空，走默认策略且需记录的情况 - WARN</br>
4. 信息记录 - INFO</br>
#### util
1. 捕获异常日志记录 - ERROR</br>
2. 参数校验为空，走默认策略且需记录的情况 - WARN</br></br>
3. 信息记录 - INFO</br>

## Sample Data
sample log data
```
{"timestamp":"2020-03-05 11:28:28.397","level":"ERROR","thread":"qtp1356236848-51326","class":"com.companyname.api.demand.DemandAssessmentControllerV2_0","message":"{\"exception\":\"java.lang.NumberFormatException\",\"desc\":\"func[getAssessmentResultApp],432903339471159296执行锁DemandAssessmentControllerV2_0#getAssessmentResultApp报错\"}","service_name":"servicename","stack_trace":"java.lang.NumberFormatException: null\n\tat java.lang.Integer.parseInt(Integer.java:542)\n\tat java.lang.Integer.valueOf(Integer.java:766)\n\tat com.companyname.service.demand.DemandAssessmentServiceV2_0.getAllRoleBaseMsg(DemandAssessmentServiceV2_0.java:1037)\n\tat com.companyname.service.demand.DemandAssessmentServiceV2_0.analyticAnswer(DemandAssessmentServiceV2_0.java:1159)\n\tat com.companyname.service.demand.DemandAssessmentServiceV2_0.getAssessmentResult(DemandAssessmentServiceV2_0.java:2747)\n"}
```
## logstash filter
```
input {
  kafka {
    bootstrap_servers => "alihn1-opd-elk-01:19092"

    security_protocol => "SSL"
    ssl_keystore_location => "/sdata/usr/local/kafka/ca/client/client.keystore.jks"
    ssl_key_password => "passed"
    ssl_keystore_password => "passed"
    ssl_truststore_location => "/sdata/usr/local/kafka/ca/trust/client.truststore.jks"
    ssl_truststore_password => "passed"


    topics => ["prdservicename01"]
    codec => "json"
    decorate_events => true
    type => "prd-servicename"
  }

  kafka {
    bootstrap_servers => "alihn1-opd-elk-01:19092"

    security_protocol => "SSL"
    ssl_keystore_location => "/sdata/usr/local/kafka/ca/client/client.keystore.jks"
    ssl_key_password => "passed"
    ssl_keystore_password => "passed"
    ssl_truststore_location => "/sdata/usr/local/kafka/ca/trust/client.truststore.jks"
    ssl_truststore_password => "passed"


    topics => ["prdservicename02"]
    codec => "json"
    decorate_events => true
    type => "prd-servicename"
  }
}

filter {

  if [type] == "prd-servicename" {
    if "servicename" in [tags] and "log" in [tags]{
      json{
        source => "message"
        target => "servicename"
      }
      json{
        source => "[servicename][message]"
        target => "[servicename][msg]"
      }
      mutate {
        remove_field => "[servicename][message]"
      }
    }
  }

}

output {
  elasticsearch {
    hosts => ["10.0.0.152:19200","10.0.0.153:19200","10.0.0.154:19200"]
    index => "%{[type]}-%{+YYYY.MM.dd}"
    workers => 1
    user => "elastic"
    password => "passwd"
  }
}
```
## kibana output
```
{
  "_index": "prd-servicename-2020.03.05",
  "_type": "_doc",
  "_id": "zKu9qHABF6ha_Oy4REWA",
  "_version": 1,
  "_score": null,
  "_source": {
    "type": "prd-servicename",
    "ecs": {
      "version": "1.0.0"
    },
    "tags": [
      "alihn1",
      "prd",
      "servicename",
      "log"
    ],
    "@timestamp": "2020-03-05T03:28:28.636Z",
    "message": "{\"timestamp\":\"2020-03-05 11:28:28.397\",\"level\":\"ERROR\",\"thread\":\"qtp1356236848-51326\",\"class\":\"com.companyname.api.demand.DemandAssessmentControllerV2_0\",\"message\":\"{\\\"exception\\\":\\\"java.lang.NumberFormatException\\\",\\\"desc\\\":\\\"func[getAssessmentResultApp],432903339471159296执行锁DemandAssessmentControllerV2_0#getAssessmentResultApp报错\\\"}\",\"service_name\":\"servicename\",\"stack_trace\":\"java.lang.NumberFormatException: null\\n\\tat java.lang.Integer.parseInt(Integer.java:542)\\n\\tat java.lang.Integer.valueOf(Integer.java:766)\\n\\tat com.companyname.service.demand.DemandAssessmentServiceV2_0.getAllRoleBaseMsg(DemandAssessmentServiceV2_0.java:1037)\\n\\tat com.companyname.service.demand.DemandAssessmentServiceV2_0.analyticAnswer(DemandAssessmentServiceV2_0.java:1159)\\n\\tat com.companyname.service.demand.DemandAssessmentServiceV2_0.getAssessmentResult(DemandAssessmentServiceV2_0.java:2747)\\n\"}",
    "fields": {
      "log_source": "prd-servicename-01"
    },
    "host": {
      "name": "alihn1-prd-servicename-03"
    },
    "@version": "1",
    "agent": {
      "hostname": "alihn1-prd-servicename-03",
      "type": "filebeat",
      "id": "f8063e8e-8b0a-4850-9d7c-8d259b36141b",
      "version": "7.1.0",
      "ephemeral_id": "2638885a-d5ec-4c5f-9114-10b552128d11"
    },
    "log": {
      "file": {
        "path": "/sdata/var/log/servicename/servicename.log"
      },
      "offset": 30326781
    },
    "input": {
      "type": "log"
    },
    "servicename": {
      "level": "ERROR",
      "stack_trace": "java.lang.NumberFormatException: null\n\tat java.lang.Integer.parseInt(Integer.java:542)\n\tat java.lang.Integer.valueOf(Integer.java:766)\n\tat com.companyname.service.demand.DemandAssessmentServiceV2_0.getAllRoleBaseMsg(DemandAssessmentServiceV2_0.java:1037)\n\tat com.companyname.service.demand.DemandAssessmentServiceV2_0.analyticAnswer(DemandAssessmentServiceV2_0.java:1159)\n\tat com.companyname.service.demand.DemandAssessmentServiceV2_0.getAssessmentResult(DemandAssessmentServiceV2_0.java:2747)\n",
      "msg": {
        "exception": "java.lang.NumberFormatException",
        "desc": "func[getAssessmentResultApp],432903339471159296执行锁DemandAssessmentControllerV2_0#getAssessmentResultApp报错"
      },
      "thread": "qtp1356236848-51326",
      "class": "com.companyname.api.demand.DemandAssessmentControllerV2_0",
      "timestamp": "2020-03-05 11:28:28.397",
      "service_name": "servicename"
    }
  },
  "fields": {
    "@timestamp": [
      "2020-03-05T03:28:28.636Z"
    ]
  },
  "highlight": {
    "log.file.path": [
      "/@kibana-highlighted-field@sdata@/kibana-highlighted-field@/@kibana-highlighted-field@var@/kibana-highlighted-field@/@kibana-highlighted-field@log@/kibana-highlighted-field@/@kibana-highlighted-field@servicename@/kibana-highlighted-field@/@kibana-highlighted-field@servicename.log@/kibana-highlighted-field@"
    ],
    "servicename.level": [
      "@kibana-highlighted-field@ERROR@/kibana-highlighted-field@"
    ],
    "servicename.thread": [
      "@kibana-highlighted-field@qtp1356236848@/kibana-highlighted-field@-@kibana-highlighted-field@51326@/kibana-highlighted-field@"
    ]
  },
  "sort": [
    1583378908636
  ]
}
```
