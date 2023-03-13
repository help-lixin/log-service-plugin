### 项目背景
系统日志用来辅助开发人员,进行问题排查,最早的日志收集是运维人员运用一些开源的框架去实现,比如:Logstash/Flume,但是,这些组件,有一定的要求,即:需要一个独立的进程去对日志文件进行监听,特别是容器化后,变得更加的复杂了(可以用:"边车"模式,或,K8S的DaemonSet模式),感觉这样去收集日志,有点重.  
当然,也有框架(Logstash)实现了日志系统接口(Appender),但是,需要开发人员通过xml去配置扩展的:Appender,感觉:有点侵入式,不符合开箱即用的想法. 

### 实现要求
1. jar包加入进来,默为认功能即可用. 
2. 日志收集时,日志的传输层可以随意组合,比如:Kafka/RocketMQ/Database/NoSQL/Http...

### 实现原理
1. 对Appender进行扩展.
2. 在Spring初始化时,找到LogContext,添加自定义:Appender(省去开发人员在xml中配置Appender). 
3. Appender最终目的是要进行日志的落地,所以:定义日志落地的,接口和业务模型. 
4. 各实现层,对接口和业务模型进行实现(http/kafka/rocketmq...)
5. 开发人员只需要添加:接口(业务模型) + 实现层依赖,即可实现:日志的收集落地. 

### 项目结构
```
log-service-plugin
    log-service-plugin-core                  # 日志核心层
    log-service-plugin-api-parent            # 日志api parent
        log-service-plugin-api               # api定义
        log-service-plugin-http              # 通过http传输日志
        log-service-plugin-rocketmq          # 通过rocketmq传输日志
        log-service-plugin-kafka             # 通过kafka传输日志
        log-service-plugin-elastcisearch     # 通过es落地日志
        log-service-plugin-mongodb           # 通过mongodb落地日志
```
