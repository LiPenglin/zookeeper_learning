# Consistency protocol

## Two Phase Commit

分布式事务处理，保证分布式系统数据的一致性.

### Description

- phase 1
> The coordinator send request to each  server ask if commit.

> Each server writes its data records to the log, Undo and Redo.

> Server return success or failure.

- phase 2
> After all participants respond OK, coordinator send commit instruction to each server.

> Each server writes the commit record to its log for reference and send success response. If fails send failure.

> Coordinator receive success, finish commit;Coordinator receive fail, send roll back transaction instruction to all servers. After servers finish roll back, each send feedback that completed.

> Servers release resource when coordinator finish commit or roll back completed.

### Constructive 

1. 所有Server的操作一直处于同步阻塞
2. 单点问题：phase 2 协调者宕机，参与者一直锁定资源
3. 数据不一致：脑裂或协调者宕机
4. 容错机制不完善

## 3PC

### Description

- Phase 1 canCommit

> Coordinator asks all participants if they can commit or not.

- Phase 2 preCommit

> After unanimously receiving yes, coordinator asks all participants to prepare to commit.

> All participants write data record to log, Undo and Redo.

> send response.

- Phase 3 doCommit

> Coordinator receives yes from all participants, send do commit instruction to them.

> Write commit record to log, return success or failure.

> Coordinator receive success, finish commit;Failure send roll back instruction;

> Servers release resource when coordinator finish commit or roll back completed.

### Better

通过canCommit 降低参与者阻塞范围;能够在单点问题之后继续运转

### Constructive

preCommit 之后，协调者宕机或者与参与者之间网络故障（网络分区），参与者无法收到协调者doCommit 指令或者roll back指令，参与者会在等待超时之后继续事务执行，所以会导致数据不一致.

## Paxos

分布式系统如何就某一个状态（提案）达成状态.

### 拜占庭将军问题

将军之间传递行动计划，这个通信员是否为叛徒，进行消息篡改.

### 兼职会议

议员与信使都是兼职身份，那么如何保证法令依然可以产生.

### paxos 算法中的角色

1. proposer 提案提出者
2. acceptor 提案裁决;只能批准一个提案;过半批准原则;
3. learner 学习提案

### 算法描述

1. prepare 阶段（生成提案）

> proposer 提出编号为Mn 的提案，向acceptor 集合发送prepare 请求；

> acceptor 反馈；

>> 保证不再接收编号小于Mn 的提案；

>> 返回已经批准的，编号最大的提案的Value；

>> 若Mn 小于已经批准的最大编号，则忽略Mn；（优化）　　

> proposer 收到过半的响应，则发送accept 请求（Mn，Vn）给acceptor 集合；Vn 是接收回的最大编号提案的Value；若无Value 可选（acceptor 都未接收提案），Vn 可为任意值；

2. accept 阶段（批准提案）

> acceptor 接收到accept 请求之后，只要Mn 不小于已接受提案最大的编号，则批准提案；

### learner 获取提案（学习策略）

1. 一旦提案被批准（过半），每个acceptor 发送给所有learner；
2. 提案批准，则统一发送给一个learner，该learner再发送给其他learner；
3. 提案批准，则统一发送给一个learner集合，该learner集合再发送给其他learner；

### 优化

> 问题：proposer1 与 proposer2 两者陷入死循环；

> 解决：选出主proposer，只要主proposer 和 过半acceptor 能保持正常，那么但凡主proposer 能提出一个编号更高的提案，这个提案最终将会批准；

### Advantage

> 支持角色转换，很大程度上避免单点问题导致的无限等待问题与脑裂问题导致的数据不一致问题

> 过半提交策略可以完善容错机制