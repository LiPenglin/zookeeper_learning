# Distributed system architecture

## Centralization to distributed

### Centralization

>__集中式系统__: 由一台或者多台计算机组成一个中心节点，数据集中存储到这个节点，系统所有业务都部署到这个节点，系统所有的功能集中处理。无需对服务进行多节点部署，无需考虑多个节点之间的协作问题。

### Distributed

#### 分布式系统

>_一个硬件或者软件分布在不同的网络计算机上，彼此之间仅仅通过消息传递进行通信与协调的系统_。

#### 面对的问题

>- 通信异常

>>消息丢失，消息延时。

>- 网络分区

>>脑裂问题

>- 三态

>>success，error，timeout

>>sender error or responser error. 

>- 节点故障

>> 宕机

## ACID to CAP/BASE

### ACID

#### 事务(Transaction)

>访问更新系统数据的操作序列

#### 原子性 Atomicity

>Program Unit 在一次执行过程中只允许出现两种状态 i 全部执行成功。 ii 全部失败。

#### 一致性 Consistency

>当数据库中只包含成功事务提交的结果时，就能说数据库处于一致性状态

#### 隔离性 Isolation 

> read uncommitted.

> read committed. 能够在避免脏读的同时保证较好的并发性能。

> repeatable read.

> serializable.

#### 持久性 Durability

### CAP & BASE

#### CAP Theorem

> _A distributed system can only have 2 of 3:Consistency, Availability and Partition Tolerance._

##### High Consistency 

##### High Availability 

Availability in a distributed system requires that system remains operational 100% of the time. Every client can get a response on time.

##### Partition Tolerance 

Have a malfunctioning node and result is one partition network affected, but the system continues to run despite some messages being delayed. Partition Tolerance is not an option, it's a necessity. We have to trade between Consistency and Availability.

#### BASE 

##### Basically Availability

- loss response time.

- degradation.

##### Soft State

Intermediate state or being delayed for synchronous data.

##### Eventual Consistency
