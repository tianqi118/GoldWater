# 咱们来聊聊CAP和BASE理论

&emsp;&emsp;在接触分布式事务的时候，往往离不开两个理论，那就是CAP和BASE理论。

#### 什么是CAP理论？

C：**一致性(Consistence)**。所有节点访问的都是同一份最新的数据副本。

A：**可用性(Availability)**。在集群中一部分节点故障后，集群整体是否还能响应客户端的读写请求。

P：**分区容错性(Tolerance of network Partition)**。以实际效果而言，分区相当于对通信的时限要求。系统如果不能在时限内达成数据一致性，就意味着发生了分区的情况。

C和A相对于容易理解，P的含义我们可以进一步从"分区"和"容错"的角度来理解。

&emsp;&emsp;一个分布式系统中由若干节点组成，每个节点之间通过网络进行通信。当有些节点之间因网络原因不能继续通信时，就会形成多个孤立的节点，称为"**网络分区**"或"**脑裂**"。

&emsp;&emsp;容错是一种分布式的能力，我们要做到节点之间的网络异常发生后整个分布式系统仍然是可用的。如果数据只存储在一个节点上，那么当"网络分区"现象出现后，与这个节点时区通信能力的所有节点都访问不到数据了。

&emsp;&emsp;为了提高分布式系统的容错能力，会把数据复制到分布式系统的所有节点上面，当"网络分区"现象再次出现后，每个节点上面的请求都能够访问数据，保证了可用性，即通过提高分区容错性来保证分布式系统的可用性。

**思考**🤔：**CAP能否同时满足呢**？

&emsp;&emsp;答案：**不可以**。如果在分布式系统中，假设有两台服务器serverA和serverB，客户端往serverA写数据，但是serverB还没有来及同步数据，客户端发起读请求，势必会造成读取的数据会不一致，这就满足了可用性。但是你要保证一致性，就必须锁住serverB，只有数据同步后，才可以放开serverB的读写，那这种就没有可用性可言了。然后，P(分区容错)呢？假如serverA和serverB两台服务器分别在中国和美国，这两台服务器可能无法通信，在系统设计的时候，必须要考虑到这种情况，一般来说，分区容错无法避免，因此可以认为CAP中的P总是成立的。所以，CAP是不能同时满足，CA存在矛盾，只能存在CP和AP。

**造成通信故障的原因有：1、GC的STW阻塞太长；2、服务器CPU利用率达到100%；3、网络不稳定因素**。

#### 什么是BASE理论？

&emsp;&emsp;为了追求C(**一致性**)和A(**可用性**)，在CAP定理的基础上逐步演化成BASE理论，其核心思想是即使无法做到强一致性，但每个应用都可以根据自身的业务特点，采用适当的方式来试系统达到最终一致性。

&emsp;&emsp;BASE理论是指**基本可用(Basically Available)**、**软状态(Soft State)**和**最终一致性(Eventual Consistency)**

**基本可用(Basically Available)**：分布式系统出现故障的时候，允许损失一部分可用性，拿响应时间和功能上的损失来换取可用。

**软状态(Soft State)**：就是说在订单系统中，在下单完成进行支付的过程中，我们可以让页面显示"支付中"，等待支付系统彻底同步完数据，订单系统才显示支付完成。即允许系统存在中间状态，这个中间态不会影响系统整体可用性。

**最终一致性(Eventual Consistency)**：它可以理解为是一种弱一致性。在允许出现中间状态的情况下，经过一段时间之后，所有数据状态才最终达到一致，从而在页面上显示为"支付成功"。

>该文章总结于《架构修炼之道》