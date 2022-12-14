## CAP原则

分布式系统有三大特性

* 一致性（consistency）：多个数据副本是否能够保持一致
* 分区容忍性（partition tolerance）：分布式系统的各个节点，任意节点网络故障时，系统仍需要可以对外提供服务
* 可用性（availability）：每次请求都可以获得正确的响应，但无法保证获取的数据是否最新

分布式系统不可能同时满足这三个特性，最多只能同时满足两项，分区容忍性必不可少，因为需要总是假设网络不是可靠的。

## 分布式一致性协议raft


## 一致性哈希算法

一致性hash就是 计算每个分布式服务器落点的算法

在分布式系统中，为了保证负载均衡，可以对发送的信息进行hash映射，然而，如果节点增加或减少，将会使得系统所有的数据节点映射改变，给系统带来不稳定性。一致性哈希对2的32次方取模，节点增加或减少对系统的影响很小。若增加或减少节点，造成不命中，只需在哈希环上顺时针找到最近的一个节点即可。

由于哈希环节点可能稠密不同，为了保证负载均衡，可以对称增加虚拟节点。

## 分布式锁ZooKeeper

分布式锁，是控制分布式系统之间同步访问共享资源的一种方式。在分布式系统中，常常需要协调他们的动作。如果不同的系统或是同一个系统的不同主机之间共享了一个或一组资源，那么访问这些资源的时候，往往需要互斥来防止彼此干扰来保证一致性，在这种情况下，便需要使用到分布式锁。
