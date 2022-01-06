# Rocketmq--消息驱动

## 什么是MQ

MQ（Message Queue）是一种跨进程的通信机制，用于传递消息。通俗点说，就是一个先进先出的数
据结构。

![](_image/什么是MQ.png)

## MQ的应用场景

### 异步解耦

最常见的一个场景是用户注册后，需要发送注册邮件和短信通知，以告知用户注册成功。传统的做法如下：

![](_image/传统邮件发送.png)

此架构下注册、邮件、短信三个任务全部完成后，才返回注册结果到客户端，用户才能使用账号登录。
但是对于用户来说，注册功能实际只需要注册系统存储用户的账户信息后，该用户便可以登录，而后续
的注册短信和邮件不是即时需要关注的步骤。
所以实际当数据写入注册系统后，注册系统就可以把其他的操作放入对应的消息队列 MQ 中然后马上返
回用户结果，由消息队列 MQ 异步地进行这些操作。架构图如下：

![](_image/MQ场景.png)

异步解耦是消息队列 MQ 的主要特点，主要目的是减少请求响应时间和解耦。主要的使用场景就是将**比
较耗时而且不需要即时（同步）**返回结果的操作作为消息放入消息队列。同时，由于使用了消息队列
MQ，只要保证消息格式不变，消息的发送方和接收方并不需要彼此联系，也不需要受对方的影响，即
解耦合。

### 流量削峰

流量削峰也是消息队列 MQ 的常用场景，一般在秒杀或团队抢购(高并发)活动中使用广泛。在秒杀或团
队抢购活动中，由于用户请求量较大，导致流量暴增，秒杀的应用在处理如此大量的访问流量后，下游
的通知系统无法承载海量的调用量，甚至会导致系统崩溃等问题而发生漏通知的情况。为解决这些问
题，可在应用和下游通知系统之间加入消息队列 MQ。

![](_image/流量削峰.png)

秒杀处理流程如下所述：
1. 用户发起海量秒杀请求到秒杀业务处理系统。
2. 秒杀处理系统按照秒杀处理逻辑将满足秒杀条件的请求发送至消息队列 MQ。
3. 下游的通知系统订阅消息队列 MQ 的秒杀相关消息，再将秒杀成功的消息发送到相应用户。
4. 用户收到秒杀成功的通知。

## 常见的MQ产品

| MQ产品     | 说明                                                                                                                          |
|----------|-----------------------------------------------------------------------------------------------------------------------------|
| ZeroMQ   | 号称最快的消息队列系统，尤其针对大吞吐量的需求场景。扩展性好，开发比较灵活，采用C语言实现，实际上只是一个socket库的重新封装，如果做为消息队列使用，需要开发大量的代码。ZeroMQ仅提供非持久性的队列，也就是说如果down机，数据将会丢失。 |
| RabbitMQ | 使用erlang语言开发，性能较好，适合于企业级的开发。但是不利于做二次开发和维护。                                                                                  |
| ActiveMQ | 历史悠久的Apache开源项目。已经在很多产品中得到应用，实现了JMS1.1规范，可以和spring-jms轻松融合，实现了多种协议，支持持久化到数据库，对队列数较多的情况支持不好。                                 |
| RocketMQ | 阿里巴巴的MQ中间件，由java语言开发，性能非常好，能够撑住双十一的大流量，而且使用起来很简单。                                                                           |
| Kafka    | Kafka是Apache下的一个子项目，是一个高性能跨语言分布式Publish/Subscribe消息队列系统，相对于ActiveMQ是一个非常轻量级的消息系统，除了性能非常好之外，还是一个工作良好的分布式系统。                  |

## RocketMQ的架构及概念

![](_image/RocketMQ的架构及概念.png)

> 如上图所示，整体可以分成4个角色，分别是：NameServer，Broker，Producer，Consumer。

* **Broker（邮递员）**：Broker是RocketMQ的核心，负责消息的接收，存储，投递等功能
* **NameServer（邮局）**：消息队列的协调者，Broker向它注册路由信息，同时Producer和Consumer
  向其获取路由信息Producer(寄件人)消息的生产者，需要从NameServer获取Broker信息，然后与
  Broker建立连接，向Broker发送消息
* **Consumer（收件人）** ：消息的消费者，需要从NameServer获取Broker信息，然后与Broker建立连
  接，从Broker获取消息
* **Topic（地区）**：用来区分不同类型的消息，发送和接收消息前都需要先创建Topic，针对Topic来发送
  和接收消息Message Queue(邮件)为了提高性能和吞吐量，引入了Message Queue，一个Topic可
  以设置一个或多个Message Queue，这样消息就可以并行往各个Message Queue发送消息，消费
  者也可以并行的从多个Message Queue读取消息
* **Message**：Message 是消息的载体。
* **ProducerGroup**：生产者组，简单来说就是多个发送同一类消息的生产者称之为一个生产者组。
* **ConsumerGroup**：消费者组，消费同一类消息的多个 consumer 实例组成一个消费者组。

## RocketMQ入门

### Windows下RocketMQ的安装

1. 下载RocketMQ  
  [http://rocketmq.apache.org/release_notes/](http://rocketmq.apache.org/release_notes/)  
  选择自己需要的版本，这里选择4.9.2版本  
  ![](_image/RocketMQ下载.png)
   ![](_image/RocketMQ下载1.png)
2. 解压缩 
   ![](_image/RocketMQ解压缩.png)
3. RocketMQ配置系统变量  
```text
系统变量名： ROCKETMQ_HOME
系统变量值： D:\RocketMQ\rocketmq-4.9.2（自己的mq解压目录）
```
4. 启动
   * 双击mqnamesrv.cmd运行
   * 解压后进入bin目录，在地址栏输入cmd进入命令框，输入以下命令
   
```shell
start mqbroker.cmd -n 127.0.0.1:9876 autoCreateTopicEnable=true
```

![](_image/RocketMQ启动.png)
![](_image/RocketMQ启动1.png)

### RocketMQ插件部署

1. 下载控制台
    [https://github.com/apache/rocketmq-dashboard](https://github.com/apache/rocketmq-dashboard)
2. 下载完成后进入解压目录的rocketmq-console项目  
   找到application.properties配置文件

```properties
server.address=0.0.0.0
server.port=8080
rocketmq.config.namesrvAddr=127.0.0.1:9876
```

3. 配置完成后，cmd进入rocketmq-console项目根目录，使用maven打包

```shell
mvn clean package -Dmaven.test.skip=true
```

4. 打包成功后，target目录下会生成rocketmq-console-ng-1.0.0.jar，使用java -jar命令运行这个jar包
5. 访问rocketmq监控页面[http://localhost:8080](http://localhost:8080)




