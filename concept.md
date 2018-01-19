#### 负载均衡

建立在现有网络结构之上，它提供了一种廉价有效透明的方法扩展网络设备和服务器的带宽、增加吞吐量、加强网络数据处理能力、提高网络的灵活性和可用性。其意思就是分摊到多个操作单元上进行执行，从而共同完成工作任务

- 服务端负载均衡：客户端请求到负载均衡服务器，负载均衡服务器根据自身的算法将该请求转给某台真正提供业务的服务器，该服务器将响应数据给负载均衡服务器，负载均衡服务器最后将数据返回给客服端。（nginx）以及zuul组件
- 客服端负载均衡： 基于客户端的负载均衡，简单的说就是在客户端程序里面，自己设定一个调度算法，在向服务器发起请求的时候，先执行调度算法计算出向哪台服务器发起请求，然后再发起请求给服务器，比如 spring cloud 中的ribbon组件

#### CAP定理

在分布式的环境下设计和部署系统时，有3个核心的系统需求（systemicrequirements），以一种特殊的关系存在。（他主要是谈论Web类的应用，但如今非常多的公司业务是多站点／多国家的，因此该理论同样适用于你的数据中心/LAN/WAN的设计）这3个核心的需求是：
- Consistency(一致性)，
- Availability(可用性)
- PartitionTolerance(分区容忍性)

在某时刻如果满足AP，分隔的节点同时对外服务但不能相互通信，将导致状态不一致，即不能满足C；如果满足CP，网络分区的情况下为达成C，请求只能一直等待，即不满足A；如果要满足CA，在一定时间内要达到节点状态一致，要求不能出现网络分区，则不能满足P