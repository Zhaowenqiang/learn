### spring和dubbo学习问题

#### 1、spring在启动过程中是怎么加载dubbo的
 - 通过查看dubbo-config-spring查找解决问题
 - 在这个jar包中提供了meta-inf中含有一个xsd，并且其中有一个spring.handle和spring.schema两个文件，这两个文件指定了xsd和相应的解析器的位置，这样在spring解析我们配置的dubbo相关的xml配置属性时，就可以通过spring的schema和xsd的规范将相应的类加载到spring的容器中
 - [一篇关于spring自定义扩展的文章](https://blog.csdn.net/zzg1229059735/article/details/82669955)
#### 2、spring的启动过程是什么样子的，ioc的初始化，bean的创建，在创建bean的时候关于dubbo的消费者是如何初始化的，spring在加载到dubbo消费者的bean配置属性式做了什么
 - 每一个插件和spring的集成只是将相应的对象通过spring的方式进行创建，我们也可以利用出过springioc以外的方式对dubbo进行创建，同时spring和dubbo的集成可以通过spring的注解和xml的方式加载，也可以收到通过dubbo的各种配置类进行加载，spring和dubbo的集成过程中也监听了许多spring ioc初始化以及结束时的事件，加载dubbo和销毁dubbo
 -
#### 3、schme又是什么作用，spring会认识dubbo的scheme吗
 - schema是spring使用xml方式集成的一种规范，结合xsd文件通过解析器就可以解析xml中的内容
 - 自己实现可以使用[xsd的一个小例子](https://www.cnblogs.com/eric-lin/p/4968985.html)
 - AbstractSingleBeanDefinitionParser spring的这个类提供了自定义的xsd处理
#### 4、meta-inf文件的特殊性
 - [一个meta-inf目录作用的解释](http://blog.sina.com.cn/s/blog_9075354e0101kc37.html)
 - 他的运行原理没有找相应的学习过程
#### 5、Java程序在运行过程中是不是要加载所有的jar，起码需要阅读所有依赖jar包的相关信息
 - 先读取meta-inf的信息，
 - 然后在spring中解析到这个文件的时候处理对象的初始化，是由于spring的配置文件中包含
 -
#### 6、spring在加载dubbo的流程
 - dubbo的一个application，registry，reference
 - 通过meta-inf获取到spring中相关schema和xsd的信息，以及他们的解析和命名空间类
 - 通过解析类和命名空间类加载配置文件，也就是dubbo配置文件的加载，其中包含xml和注解的方式
 -

#### 7、xsd的语法学习
 -
#### 8、spring中dubbo的退出事件处理
 - dubbo监听了spring的退出事件，在spring容器退出时对dubbo的进行退出
 - DubboShutdownHook，这个方法的销毁函数，这个对象是静态单例处理的
#### 9、为什么在这里特殊处理，ConfigurableApplicationContext这个有什么特殊的，什么作用
 - ConfigurableApplicationContext为ApplicationContext的直接子类，处理它就代表处理所有的类
#### 10、dubbo通过ServiceBean和ReferenceBean中将自己注册进去
 - FactoryBean
