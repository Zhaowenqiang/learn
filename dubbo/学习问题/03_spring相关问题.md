### dubbo学习过程中的spring问题

#### 1、spring的监听事件都有哪些，分别有什么用
 - ApplicationContextEvent
 - ContextClosedEvent
 - ContextRefreshedEvent
 - ContextStartedEvent
 - ContextStoppedEvent
 - PayloadApplicationEvent
 - RequestHandledEvent
 - ServiceBeanExportedEvent dubbo
 - ServletRequestHandledEvent
#### 2、Java中的EventObject，EventListener，是做什么用的
 - 特别简单，但是不知道为什么需要这么用，是Java内部这么要求的吗
 - [简单实现](https://www.cnblogs.com/minisculestep/p/5059321.html)
 - 在spring中ApplicationListener，ApplicationEvent这两种类和接口，处理容器相关的事件
#### 3、事件监听器中的源在什么地方，spring的哪里处理的
 -
#### 4、spring的ApplicationContext都有哪些实现类，之间有什么区别
 - ApplicationContext表示spring的上下文环境，存储了容器的相关信息
 -
#### 5、Java中提供了对应的退出钩子，spring是利用这个钩子处理自己的退出事件，spring的退出又是什么样子的，原理和运行机制
#### 6、java中关于退出钩子的处理是什么原理，运行机制是什么样子的
#### 7、
