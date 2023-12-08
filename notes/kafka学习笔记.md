Kafka 学习笔记  
  
# 资源  
> [APACHE KAFKA](https://kafka.apache.org/)  
  
  
# 消息队列  
> [What is a message queue?](https://www.ibm.com/topics/message-queues)  
> [What is Message Queuing?](https://www.cloudamqp.com/blog/what-is-message-queuing.html)  
  
  
> A message queue is a component of messaging middleware solutions that enables independent applications and services to exchange information.  
  
> Message queues store “messages”—packets of data that applications create for other applications to consume—in the order they are transmitted until the consuming application can process them.   
  
> Message Queuing enables asynchronous communication between different components or systems, in order to increase system reliability, scalability, and flexibility.  
  
## 使用场景  
- 削峰填谷  
如电商业务中秒杀等活动时，防止系统超负荷而崩溃，可以使用消息队列存储请求，然后服务器慢慢消费处理  
  
- 异步解耦  
将系统中的不同组件或模块解耦，通过使用消息队列或异步通信方式来实现它们之间的解耦合。  
  
通过使用消息队列，系统的不同组件可以通过消息进行通信，而不需要直接依赖于彼此的接口和实现细节。  
  
如在一个电子商务平台的订单处理系统中，当用户下单时，可以将订单信息发送到消息队列中进行异步处理。这样，前端系统可以立即响应用户的下单请求，而后续的订单处理、库存更新、支付处理等可以在后台异步进行，提高系统的响应性能和可伸缩性。  
  
异步解耦也适用于处理复杂的业务流程，其中涉及多个步骤和多个参与者。通过将每个步骤的结果作为消息发送到队列中，可以实现流程的解耦和灵活的扩展。每个步骤的参与者可以独立地处理消息，并将结果传递给下一个步骤。  
  
- 蓄流压测  
堆积一定消息再开放进行压测  
  
## 主流 MQ  
- Kafka  
- RabbitMQ  
- RoketMQ  
- ActiveMQ  
  
# 数据流  
数据流是指一系列连续的数据元素按照时间顺序产生或传递的流动数据。它可以是实时生成的数据，也可以是从数据源传输到数据接收者的数据流。  
  
数据流可以包含各种类型的数据，例如传感器数据、日志数据、交易数据、网络流量、用户行为数据等。这些数据可以是连续的、离散的、结构化的或非结构化的。  
  
数据流的特点是持续不断地产生或传输数据，而且数据的到达速率可能是不均匀的。数据流通常具有以下几个关键属性：  
  
1. 持续性：数据流是连续不断地产生或传输数据，没有明确的开始或结束点。新的数据元素会持续地加入到数据流中。  
  
2. 实时性：数据流中的数据具有时间敏感性，需要在一定的时间窗口内进行处理和分析，以便及时响应和作出决策。  
  
3. 无限性：数据流的长度可以是无限的，因为数据流可以持续地产生或传输。因此，处理数据流需要采用适当的策略来处理无限数据流的挑战。  
  
4. 可变性：数据流中的数据可能会以不同的速率到达，数据的到达速率可能是不均匀的，需要处理这种变化性。  
  
数据流处理是指对数据流进行实时处理和分析的过程。数据流处理系统可以从数据流中提取有用的信息，进行实时计算、过滤、聚合、转换和模式识别等操作，以支持实时决策和业务需求。  
  
# 事件流  
> [What is event streaming?](https://kafka.apache.org/intro)  
  
Event streaming is the practice of capturing data in real-time from event sources like databases, sensors, mobile devices, cloud services, and software applications in the form of streams of events.  
  
事件流是指一系列以事件为单位的数据流。每个事件代表系统中发生的某个有意义的动作或状态变化，例如用户的操作、传感器数据的采集、业务流程的进展等。事件流可以包含多个事件，每个事件都有自己的唯一标识符和相关的数据。  
  
# Kafka  
> [APACHE KAFKA](https://kafka.apache.org/)  
  
Kafka是一种分布式流处理平台，用于处理事件流，它的主要目标是提供高吞吐量、低延迟的持久化消息系统，用于处理实时数据流和构建实时数据管道。  
  
在Kafka中，事件以消息的形式进行传递和存储。每个事件被封装为一个消息，并发布到一个或多个主题（topics）中。主题可以看作是事件流的逻辑分类，可以有多个消费者订阅一个主题，并从中读取事件。  
  
Kafka的分区和副本机制使得事件流可以进行并行处理和冗余存储。主题被分成多个分区，每个分区都有自己的消息序列，并可以在多个服务器节点上存储多个副本，以提供高可用性和故障容错。  
  
## Kafka 架构  
> [Apache Kafka Architecture and Its Components-The A-Z Guide](https://www.projectpro.io/article/apache-kafka-architecture-/442)  
  
- Kafka Cluster  
> A Kafka cluster is a distributed system composed of multiple Kafka brokers working together to handle the storage and processing of real-time streaming data. It provides fault tolerance, scalability, and high availability for efficient data streaming and messaging in large-scale applications.  
  
kafka 集群由多个 brokers 组成  
  
- producer  
生产消息，生产的消息发布到 broker  
  
- consumer  
消费消息  
  
- consumer group  
每个 consumer 属于一个 consumer group  
  
A consumer group in Kafka refers to a number of consumers that pull data from the same topic or same set of topics.   
  
每个消费者组都有一个特定的消费者，称为消费者组协调器（Consumer Group Coordinator）。协调器负责管理和分配分区给消费者组中的消费者，并确保每个分区只由一个消费者进行消费。  
  
通过将多个消费者组成消费者组，可以实现消息的并行处理和扩展性。每个消费者可以独立地消费分配给它的分区，从而提高整体的消费吞吐量。  
  
  
在同一个消费者组中，每个分区只能由一个消费者消费。  
  
不同的消费者组可以独立地消费同一个主题中的消息。这意味着同一个分区的消息可以被不同的消费者组消费。每个消费者组都会独立地管理自己的消费者和分区分配，不会干扰其他消费者组的消费进程。  
  
- broker  
broker 为 kafka 集群中一个服务器节点，每个 broker 是 kafka 一个实例，用于存储和处理消息  
  
- topic  
一个 topic 可以看成一个有唯一名称的消息类别，数据以 topic 的形式存储，是消息的逻辑分类，用于组织和管理消息的发布和订阅。  
  
- partition   
每个 topic 可以分割为一个或多个 partition，分区物理上表现为一个个文件夹，可以实现负载均衡，一般分区的数目不超过节点数  
  
同一个分区内的消息是按顺序进行处理的，但不同分区的分区则是无序的  
  
- replication  
数据的副本，包括 leader 和 follower 的副本  
  
AR: assigned replicas，分区的所有副本，leader + follower 的集合  
ISR: in sync replicas，所有与 leader 保持同步的 follower 副本和 leader 的集合  
OSR: out-of-sync replicas, 所有未与 leader 保持同步的 follower 的集合  
  
- leader and follower  
一个分区如果有多个副本，其中一个副本为 leader，用于处理读写任务，其余的副本为 follower，用于从 leader 复制数据同步，当 leader 节点出故障时，其中一个 follower 节点中的该副本变为 leader 角色  
  
## 部署 Kafka  
> [APACHE KAFKA QUICKSTART](https://kafka.apache.org/quickstart)  
  
## 图形管理工具 Offset Exploer  
> [Offset Explorer](https://www.kafkatool.com/)  
  
管理 kafka 集群的工具  
  
## 集群监控系统 kafka-eagle  
> [KAFKA EAGLE](http://www.kafka-eagle.org/)  
