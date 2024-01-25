ZooKeeper 学习笔记  
    
# 资源  
> [zookeep 官网](https://zookeeper.apache.org/doc/r3.9.1/index.html)  
    
# ZooKeeper 介绍  
> [Conditional updates and watches](https://zookeeper.apache.org/doc/r3.9.1/zookeeperOver.html#Conditional+updates+and+watches)  
  
ZooKeeper is a distributed, open-source coordination service for distributed applications. It exposes a simple set of primitives that distributed applications can build upon to implement higher level services for synchronization, configuration maintenance, and groups and naming.  
    
ZooKeeper 是一个开源的分布式协调服务，主要用来解决分布式应用中经常遇到的数据管理问题，如：命名服务、状态同步、配置中心、集群管理等。  
    
- 命名服务  
命名服务是一种将名称映射到网络资源的机制，使得应用程序能够通过名称来访问这些资源。  
命名服务是基于 ZooKeeper 的层级名称空间结构  
    
- 配置中心  
配置中心是一种集中管理和动态更新应用程序配置的机制，它可以帮助应用程序实现配置的集中化管理、动态加载和实时更新。  
    
# ZooKeeper 数据节点 Znode  
> [Data model and the hierarchical namespace](https://zookeeper.apache.org/doc/r3.9.1/zookeeperOver.html#sc_dataModelNameSpace)  
    
ZooKeeper提供了一个分层的命名空间，类似于文件系统的目录结构。它使用称为"znodes"的数据节点来存储和管理命名服务。每个znode都可以包含数据以及与其相关的 ACL（访问控制列表）信息。  
    
Znodes maintain a stat structure that includes version numbers for data changes, ACL changes, and timestamps, to allow cache validations and coordinated updates. Each time a znode's data changes, the version number increases. For instance, whenever a client retrieves data it also receives the version of the data.  
    
znode 是 zookeeper 的数据节点，存放协调数据，通常这些数据占用空间很小。  
ZooKeeper was designed to store coordination data: status information, configuration, location information, etc., so the data stored at each node is usually small, in the byte to kilobyte range.  
    
znode 中的数据的读写都是原子操作，且含有 ACL 信息进行访问控制。  
    
znode 也可以是临时节点，会话期间有效，会话结束后删除。  
    
ZooKeeper data is kept in-memory, which means ZooKeeper can achieve high throughput and low latency numbers.  
    
## 集群管理  
- 集群监控  
对集群运行时的状态收集  
- 集群控制  
对集群进行操作和控制  
    
# ZooKeeper 集群  
ZooKeeper 集群是基于 master/slave 模型  
    
集群中有一半节点正常，则可对外提供服务，集群数量最好为奇数  
    
## 集群角色  
- leader  
处理写请求，事务请求的唯一调度者和处理者，负责投票发起，更新系统状态  
写操作完成后同步到 follower 节点，且同步节点数过半才认为写操作成功  
- follower  
负责读操作，在选举 leader 过程中参与投票  
- observer  
和 follower 区别是不参与 leader 投票和写操作的“过半写成功”策略，提升集群读性能  
- learner  
和 leader 进行状态同步的节点统称为 learner，包括 follower 和 observer  
    
## 节点角色状态  
- LOOKING  
寻找leader状态，处于竞选 leader 过程中  
- LEADING  
领导者状态，该角色已经是 leader  
- FOLLOWING  
跟随者状态，该角色是 follower  
- OBSERVER  
观察者状态，该角色是 observer  
    
## Leader 选举过程  
> [13.0 Zookeeper Leader 选举原理](https://www.runoob.com/w3cnote/zookeeper-leader.html)  
    
    
- zxid  
zookeeper transaction id，每个改变 zookeeper 状态的操作都会生成一个对应的 zxid，最大的优先选为 leader  
- myid  
服务器的唯一标识，zxid 相同时，myid 大的优先选为 leader  
成为 leader 的必要条件：zxid 最大，得到超过半数节点的选票  
    
- 心跳检测  
leader 和 follower 利用 ping 来检测对方存在，当 leader 无法无法响应时，将重新发起 leader 选举  
如果是 follower 的问题而非 leader 的问题呢？  
当 leader 服务器出现网络故障或其他异常时，ZAB（zookeeper atomic broadcast） 协议进入恢复模式并选举新 leader：  
- leader election  
- discovery  
新 leader 和 follower 通信  
- synchronization  
新 leader 同步集群副本数据，保证集群数据一致性  
- broadcast  
集群对外提供服务  
