
# netty 问题整理

 ### 1、nio,bio,aio区别
 - bio为同步阻塞式io
 - nio为同步非阻塞式io
 - aio为一部非阻塞式io
 ### 2、为什么netty没有使用aio,而是使用nio
  - aio的内部实现利用了操作系统的回调形势，这种方式目前的性能没有nio的高
 ### 3、nio的selector和缓存作用
  - netty的selector是用来检测注册在其上的channel是否准备就绪，实现非阻塞的重要工具
  - netty的缓存重要是和channel配合使用，channel将其进行网络操作的读写数据进行缓存，配合selector实现nio的非阻塞功能
 ### 4、Java对堆内内存和堆外内存的优劣势
  - Java通过buffer的DirectBuffer实现类创建了堆外内存
  - 堆外内存的创建，不在Java虚拟机中，不受Java GC的影响，但是如果堆外内存在虚拟机中的引用被清理，对外内存也会释放
  - 堆外内存由于直接和操作系统进行操作，其空间的创建成本比较高
  - netty也是通过堆外内存实现在网络通信中的零数据copy，不同通过Java虚拟机内存，在到操作系统内存的copy工作，提高性能
 ### 5、Java中buffer数组的数据原理，读模式和写模式
 ### 6、channel在nio中的作用
  - channel是nio对于网络通信中的一个通道，内部具有一个buffer数组，可以实现通信中的读写功能
 ### 7、selector轮询多个channel之间会有影响吗
  - 由于业务线程池的存在，selector在检测到channel准备就绪之后，会将channel的读写事件交有业务线程，进行业务操作，不会相互之间有影响
 ### 8、channel就绪是有写入或者有读入，那么写入或读入一半的时候怎么处理
  - 此问题属于netty网络处理中的拆包黏包问题，在tcp操作过程中也会由于数据包的大小出现相同问题
  - 在netty中提供了四种解决实现类，两种是基于固定长度读取数据（其一是通过固定的传输长度，其二是通过报文头的指定长度），两种是基于特殊字符处理（其一是通过行读取形式，其二是通过指定消息边界的方式）
 ### 9、当channel的数量变大时，selector的轮询效率会变低，这种情况怎么处理
  - 在selector内部实现中会使用多线程
 ### 10、selector的内部多线程轮询的原理是什么
 ### 11、netty中epoll是什么原理

 ### 12、操作的系统ET模式和LT模式什么区别

 ### 13、netty的优点是什么
  - 实现简单，api操作方便
  - demo案例丰富，文档丰富，论坛活跃，解决问题方便
  - netty功能强大
  - 可定制性高
  - netty性能高，稳定
 ### 14、reactor线程模型的理解
  -
 ### 15、netty如何解决tcp的拆包和黏包
  - 问题8
 ### 16、netty如何实现高性能
  - 通过堆外内存实现零copy
  - 线程模式
  - 内存池话设计
  - 并发能力高，使用了大量的原则性操作，内部unsafe实现
  - 数据序列化方式，提供多种高效能的数据系列化
  - 使用jni的方式，选择更优的ssl引擎，以及epoll形式
  - 通过反射技术操作对象
  - 实现FastThreadLocal
 ### 17、netty的内存池话设计思路
 ### 18、netty的零copy实现
  - 通过管理堆外内存实现在网络数据传输过程中的Java虚拟机内存到操作系统内存的零copy
 ### 19、fastTheadLocal的实现原理

 ### 20、netty的高性能如何体现
  - 同问题16
 ### 21、netty的高可靠如何实现
  - 通过IdleStateHandler实现链路检测，读写检测，也可以实现心跳监控
  - 内存保护机制，对于bytebyffer的申请进行细粒度的检测，防止非法访问内存，设置内存和线程上限保护，防止资源耗尽宕机
  - 在系统退出后，可以通过jvm的shutdown hook关闭系统，保护运行时数据
 ### 22、netty的高可扩展如何实现
  - ChannelPipeline 的责任链模式开发，便于业务程序的扩展
  - 关键类库都实现了抽象类，或接口，方便扩展
  - 提供大量工厂类。系统可以按需创建使用对象
  - 通过了大量配合，客户可以按需进行配置
 ### 23、netty的核心组件
  - bootstrap和serverBootstrap
  - channel和channelFuture
  - EventLoop 和 EventLoopGroup
  - ChannelHandel和ChannelPipeline
 ### 24、netty的怎么解决内存泄漏
 ### 25、netty怎么降低锁竞争
 ### 26、netty的队列优化，环形数据缓存区的设计
 ### 27、
