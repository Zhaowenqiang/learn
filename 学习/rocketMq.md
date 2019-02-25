### rocketMq 学习笔记

#### 1、什么是rocketMq
#### 2、rocketMq的组成
 - 生产者（product）
 - 消费者（consumer）
 - 消息服务器（broker）
 - 名称服务器（nameServer）
#### 3、rocketMq的整体运行流程
#### 4、nameSrv的作用
#### 5、broker的工作原理
 - 高并发读写服务
 - 负载均衡与动态伸缩
 - 高可用和高可靠
 - broker和namesrv的心跳机制
#### 6、broker的基本组件
 - 远程处理模块 broker的入口，处理客户请求
 - client manager 生产者和消费者的管理端，并维护消费者的主题订阅
 - store service 简单的Api用来存储或查询物理磁盘中的消息
 - Ha 服务 提供主节点和从节点之间的数据同步
 - 索引服务 通过指定键为消息建立索引
#### 7、broker的存储结构
 - 消息生产和消息消费的相互分离
 - commitLog文件采用混合型存储，所有topic的消息写入一个commitLog日志文件中
 - 消息生产的写是顺序写，但是消息读却是随机读（通过consumeQueue加上物理位置偏移量的方式）
 - 随机读对于整体的读性能的影响比较大
#### 8、存储架构的优缺点
 - 优点
    - consumeQueue消息逻辑队列较为轻量级
    - 对于磁盘访问的串行话，避免磁盘竞争，不会因为队列增加导致IO等待
 - 缺点
    - 对于commitLog来说是顺序读，但是消息读却是随机读
    - consumer端订阅消费一条消息，需要先读consumeQueue，在读commitLog，增加开销
#### 9、什么是Mmap，有什么用
 - mmap是一种内存映射技术，可以直接将磁盘中的内容在内存中进行映射，而不需要将内存直接copy至内核缓冲区，只有当缺页发生时，才需要将文件中数据直接copy至用户态的进程空间，进行一次数据copy
 - 使用这种技术可以大大提高文件操作的读写性能
#### 10、什么是os的pageCache，基本原理是什么
 - pageCache是os对于文件缓存的一种技术，主要用来提高文件的读写性能
 - 文件读取时，如果没有命中pageCache，则会对于邻近的几个页面进行预读取
 - 文件写入时，会将文件先写入cache，之后异步刷新至物理磁盘
#### 11、rocketMq怎么利用这些特性提高性能的
 - rocketMq在一开始就将数据文件映射到os的虚拟内存中
 - 在写入数据的时候，直接将数据写入到pageCache的缓存，之后通过异步刷盘的方式将消息持久化到磁盘
 - 在进行读取的时候（随机读），由于pageCache的整体逻辑是由旧到新，所以大部分都是在pageCache中进行读取
#### 12、pageCache有什么缺点，rocketMq又是怎么解决的
 - pageCache技术在遇到os进行脏页回写，内存回收，内存swap等情况是时，会导致消息读写延迟
 - 但是rocketMq也才有用了，内存预分配，文件预热，mlock系统调用的方式尽量减少缺点带来的延迟
#### 13、rocketMq的存储模型是什么
 - commitLog 主要用来存储消息主题，默认1G大小
 - consumeQueue 消息消费的逻辑队列，包含消息队列在commitLog中的位置和偏移量
 - indexFile 生产访问数据的索引，通过消息的key值进行访问
 - MappedFileQueue 通过他快速定位mappedFile的位置进行操作
 - MappedFile 通过这个对象将数据写入pageCache，或同步刷到磁盘
#### 14、rocketMq的刷盘机制
 - 同步刷盘，但是会影响下入性能
 - 异步刷盘，可能导致数据丢失
#### 15、mmap中的缺页是什么意思
#### 16、jdk nio中国的mapperByteBuffer
 - mapperByteBuffer继承自byteBuffer，在其内部维护了一个逻辑地址变量
 - 在建立映射关系时，jdk nio的fileChannel中的map()方法把文件会映射到虚拟内存
 - 在其内部实现中都是使用的directByteBuffer进行的，其内部实现又是通过unsafe.getByte,并且以地址加上偏移量的方式进行
#### 17、神奇的unsafe，到底有什么用，好多优化操作都是通过他进行讲解的
#### 18、mmap的限制
 - mmap映射内存释放问题，由于映射内存不属于GC管理，所以映射内存无法自动卸载，而且fileChannel的unmap方法为私有的，所以rocketMq通过反射进行卸载内存
 - mappedByteBuffer内存映射，由于其占用的是虚拟内存，不受jvm限制，但是收到os虚拟内存限制，一般映射在1.5到2G
 - mappedByteBuffer会引起其他 内存占用率高和文件关闭不确定性的问题（为什么）
#### 19、内存预分配技术，预先分配MappedFile
 -
#### 20、mlock系统调用
 - 通过mlock将进程中的数据锁定到物理内存，防止交换到swap空间
#### 21、什么是swap空间
#### 22、文件预热
 - rocketMq会将尽可能多的数据映射到内存中，
#### 23、什么是messageTag,在rocketMq中有什么作用
#### 24、producer的作用
 - 获得topic-broker的映射关系，通过namesrc获取地址，并且实时心跳更新数据30s，如果producer死掉，broker每10s会检测，发现后断开连接
 - 生产者端的负载均衡，轮询方式，将消息发给每一个的broker
#### 25、producer的发送方式，oneway是什么意思
#### 26、consumer的作用
 - 获取topic-broker的映射关系，从那么srv获取broker和topic信息，30s更新一次，如果consumer死掉，broker会在10s检测一次，如果两次为检测成功，会断开连接，并向其他consumer发送进行负载均衡
 - 消费者端负载均衡，一个消费组内进行负载均衡
#### 27、消费模式
 - 集群消费
 - 广播消费
#### 28、消息重放
 - 修改consumer的offer
#### 29、顺序消息，如何实现
 - 普通顺序消息
 - 严格顺序消息
#### 30、定时消息
#### 31、消息重试，
 - 消息在消费失败之后，会添加到重试队列中，再次消费
 - 如果消息重试次数太多，则会将消息加入死信队列
#### 32、事务消息
 - 提交和回滚机制
#### 33、rocketMq的高可用实现
#### 34、rocketMq是否会弄丢数据
#### 35、保证消息消费的幂等性
#### 36、
