---
title: Interview-Note
date: 2019-10-22 22:13:57
tags:
---

## JAVA基础

1. HashMap的源码。
> 数组加链表的结构，JDK1.8之后引入红黑树，达到阀值转树或者拆树。
> 扩容是double size。保持2的N次幂。因为决定inde位置的时候用的是hashcode%(size-1)。如果是17这种当减了1之后就是(10000)，这样做&运算会导致很多index没有被利用，造成浪费。
> HashSet的底层就是用的HashMap，contains就是用的map的containsKey，add就是用的map的put，只是value用的是new Object()。 

2. ConcurrentHashMap原理，JDK1.8都有什么新特性
> HashMap是线程不安全的，HashMap在高并发下的put操作因为用头插法会导致列表死循环，jdk1.8优化过后保持和原链表顺序一致解决死循环的问题，但是仍然有数据丢失的弊端。
> HashTable是对在方法上加锁，效率不高
> ConcurrentHashMap，在1.6的时候采用 Segment+ReentrantLock（Segment继承ReentrantLock） ，每一段加锁，减少并发的冲突。但是竞争同一个锁的概率非常小，以及多个分段锁所占用的空间，所以在1.8的时候从分段锁改成CAS+Synchronized(对每个node)，对每一个node都使用volatile关键字，进一步细化了锁。

3. JAVA 四中引用类型
> 强引用
> 软引用(SoftReference)，当内存不足的时候会被gc回收
> 弱引用(WeakReference)，只要gc就被回收
> 虚引用(PhantomReference)，必须和引用队列联合使用。主要作用是跟踪对象被gc的状态。

4. Object类中的方法。
> clone(),equals(object),finalize(),getClass(),hashCode(),notify(),notifyAll(),toString(),wait()

5. 负载均衡算法除了随机和轮询以外的一致性hash算法
> 整个hash空间设计成圆环形，每个服务根据算法获得一个hash值，然后分布在圆环上，对服务的请求对象也用同样地算法分布在圆环上，然后以顺时针的方向去找自己最近的服务，然后调用。如果一个服务宕机，就顺时针往下找，如果新增的话也只影响部分请求。

<!-- more -->

6. 接口和抽象类的区别
> jdk1.8接口新增默认方法和静态方法
> jdk1.9接口增加private method 和 private static method，在有了这两个以后，接口和抽象类的区别，只剩下构造器，成员变量，以及单继承。
> 接口没有构造函数，可以多继承，成员变量只能public
> 抽象类有构造函数，单继承，成员变量可以private

7. Java序列化的方式
> 实现Serializable接口，会自动序列化所有非static和非transient的成员变量。
> 实现Externalizable接口，必须实现writeExternal()和readExternal()方法，相当于手动序列化。
> 实现Serializable接口+添加writeObject()和readObject()方法。不是override，而是添加这两个方法，不过方法的第一行要调用defaultWriteObject或者defaultReadObject，然后再自己控制。

8. 传值和传引用的区别，Java是怎么样的，有没有传值引用。
> 基本类型（byte,short,int,long,double,float,char,boolean）为传值；对象类型（Object，数组，容器）为传引用；String、Integer、Double等类型因为类被设为final，无法被修改，只能重新赋值或生成对象。

9. 一个ArrayList在循环过程中删除，会不会出问题，为什么。
> ArrayList.remove 没有更新modCount
> Iterator.remove 有更新modCount

10. Long,Integer 包装类型常量 cache 为 -128 到 127 之间

11. 缓存雪崩
> 多个缓存同时失效，请求直接发到数据库。
> 设置不一样的过期时间，或者给数据加上比缓存有效时间短的字段，每次获取的时候当字段过期就刷新缓存，相当于提前刷新缓存,避免雪崩。

12. 缓存击穿
> 比如用户不在数据库中，返回null，缓存不存的话就一直访问数据库。布隆过滤，生成一个bitmap，拦截肯定不存在的数据，或者把null也保存到缓存中，过期时间可以设置短一点。

13. 二叉查找树 -> 二叉平衡树 -> 红黑树
> 二叉查找树是按照顺序左小又大，但是会出现链式的特殊情况，使得查找的效率降低。
> 为了解决这个问题，出现了二叉平衡树，每个节点的左子树和右子树的高度差不超过1。
> 二叉树的特性导致需要不断的左旋和右旋，因此出现了红黑树。红黑数具有二叉树的特点，根节点是黑色的，叶子节点都是黑色的空节点不存数据，相邻节点不能同时为红色，每个节点到可达叶子节点的路径包含相同数目的黑色节点。

14. 单例模式
> 五种写法，饿汉，饱汉，双重检查，静态内部类，枚举
> 双重检查: getInstance方法不加锁，而是在instance为null的情况下加锁初始化，比起在getInstance方法上加锁效率要高很多，在同步块中还是要加入instacnce的空判断，所以叫双重检查。

15. 防反编译
> 可以自定义ClassLoader进行解密。把class文件进行加密。

16. Throwable
> Throwable 是超类，下层有 error 和 exception ， error 不是应用抛出的，是系统内部出错。 exception 分成 RuntimeException 和 CheckedException 。
> RuntimeException : ClassCastException， IndexOutOfBoundsException， NullPointerException， UnsupportedOperationException , ArithmeticException 
> checkedException : IOException, SQLException , ClassNotFoundException

17. 泛型的好处
> 没有泛型的时候只有Object，导致需要显式的强转类型，ClassCastException又是RuntimeException, 不容易被发现导致服务出错。

18. swicth的表达式不能为null
> 底层其实是调用表达式的hashcode方法，所以会报NullPointerException

19. 基本类型占多少bit（1字节=8bit）
> byte 8bit
> short 16bit
> int 32bit
> long 64bit
> float 32bit
> double 64bit
> char 16bit
> bool 1bit

20. 实例化顺序（都是先父后子）
> 静态变量和代码块一起->非静态变量和构造函数

## tomcat
1. tomcat加载隔离 
> tomcat的加载顺序是 BootStrapClassLoader ->  WebappClassLoader(WEB-INF/classes) ->  WebappClassLoader (WEB-INF/lib) -> CommonClassloader(tomcat\lib)。所以要用公共的jar还得把自己目录下的jar删除
> tomcat没有遵守双亲委派机制。自定义了 CommonClassloader 、CatalinaClassLoader、 SharedClassLoader,但是 tomcat/conf/catalina.properties 的默认配置只配了common，所以 CatalinaClassLoader 和 SharedClassLoader 其实是空的，不过代码中createClassLoader的时候设置了 parent 是 CommonClassloader。
> 在tomcat中都是通过扩展URLClassLoader来实现自己的类加载器。

2. tomcat结构
> 参考 https://blog.csdn.net/xlgen157387/article/details/79006434
> 一个server有多个service，一个service有多个connector，但只有一个container
> service是对外提供服务
> connector是接受请求并封装成Request和Response
> container是封装和管理servlet，以及处理request请求。
> 请求流程 ：请求->service->connector->container->connector->client
> connector使用ProtocolHandler处理请求，根据协议进行不通的处理，比如http或者socket，ProtocolHandler包含 Endpoint、Processor、Adapter。
> connector流程 ：EndPoint处理底层Socket网络请求，Processor将EndPoint接受的socket封装成Request，Adapter将request交给Container进行具体的处理。
> 一个 Container 一个Engine，一个Engine可以有多个host，host对应tomcat目录的webapps，一般就一个。一个host可以有多个Context，对应webapps下面的ROOT目录以及同级其他目录。一个context下面有多个wrapper，每一个都封装一个servlet。
> request->container->EnginePipline->EngineValve1,EngineValve2...->StandardEngineValve->HostPipeline->Hostvalve1,Hostvalve2...->StandardHostValve->ContextPipeline->ContextValve1,ContextValve2...->StandardContextValve->WrapperPipeline->WrappedValve1,WrappedValve2...->StandardWrappedValve->创建FilterChain->doFilter->Connector->client

## Spring
1. spring五种作用域
> singleton,prototype(每次使用都创建),request,session,global session(仅适用于Spring portlet MVC)

2. Spring Bean的生命周期
> 实例化(new)->ioc->setBeanName(BeanNameAware)->setBeanFactory(BeanFactoryAware)->setApplicationContext(ApplicationContextAware)->postProcessBeforeInitialization(BeanPostProcessor)->init-method(@PostConstruct)->postProcessAfterInitialization(BeanPostProcessor)->destroy(DisposableBean)->destroy-method(@preDestroy)

3. Spring IOC的四种方式
> 构造器，setter注入，静态工厂注入(工厂类的静态方法)，实例工厂注入

4. Spring AOP的应用场景
> 我所使用的场景主要是Advice，对异常的同一处理，以及对接口返回对象的统一处理。也做过对接口的请求和返回进行拦截做日志追踪。

5. spring AOP是如何实现的
> @Aspect,@PointCut,@AfterReturning,@AfterThrowing
> 是利用JDK的动态代理以及CGLIB实现的，JDK的需要目标对象实现接口，CGLIB是通过继承目标对象实现的。
> 如果实现了接口，默认是JDK，但是也可以指定CGLIB。Proxy利用 InvocationHandler 动态创建一个符合某一接口的实例，生成目标类的代理对象。
> 如果没有实现接口，只能是CGLIB。
> JDK,CGLIB 都不能用final和private修饰

6. Spring MVC原理
> HttpRequest->DispatcherServlet->HandlerMapping->controller->service->ModelAndView->DispatcherServlet->ViewResolver->Model->View->HttpResponse

7. Spring 事务
> Spring Boot中Propagation(传播行为)默认是 REQUIRED，timeout是-1，readOnly是false，isolation是DEFULT(按数据库的隔离等级)
> 事务隔离和数据库的四个隔离等级对应。
> 传播行为如下
> REQUIRED: 当前事务优先，做完再新增。
> SUPPORTS: 当前事务优先，没有就以非事务的方式执行。
> MANDATORY: 当前事务优先，没有就抛出异常。
> REQUIRES_NEW : 新建一个事务，先做新的事务，再做老的事务。
> NOT_SUPPORTED: 以非事务的方式进行，如果当前存在事务，则把当前事务挂起
> NEVER: 以非事务方式运行，如果当前存在事务，则抛出异常
> NESTED: 如果当前存在事务，则创建一个事务作为当前事务的子事务来运行。没有就新增。

8. spring 的 controller 是单例，怎么保证并发的安全。
> 不要在controller中定义成员变量，尽量是静态变量
> 可以使用ThreadLocal

9. Spring 的IOC 循环依赖
> ![]( https://hsindumas.github.io/images/IOC循环依赖.png )
> 当初始化A还没赋值的时候，发现依赖B于是先去BeanFactory获取B，没获取到就去初始化B，发现B又依赖A，这个时候从BeanFactory获取到A的早前版本，就是没有赋值完成的版本，然后给B去完成赋值，最后A完成初始化。

10. Spring设计模式
> 工厂模式
> 单例模式
> 策略模式
> 观察者模式(spring 通常用listener实现)
> 适配器模式
> 包装器模式

## JVM
1. 元空间里有些啥
> Method Area是JVM的规范，永久代是HotSpot对这种规范的实现。在 JDK 1.8 中， HotSpot 已经没有永久代了，取而代之的是 Metaspace（元空间）。元空间并不在虚拟机中，而是使用本地内存。原来永久代里面除了类的元信息之外还有RuntimeConstantPool，现在metaspace只存元信息，RuntimeConstantPool放在了Heap内。

2. JVM 什么情况下会发生栈溢出。
> JAVA stack 或者叫VM Stack ，每个方法从调用到执行完成对应着一个栈帧从入栈到出栈的过程。当栈的深度大于虚拟机允许的深度时抛出StackOverflowError。
> Native Method Stack 跟 JAVA Stack一样，值得注意的是，HotSpot已经把这两个合并了。

3. JVM 什么情况下会发生内存溢出。
> Heap是用来存放创建的对象，这个是gc最主要的地方。当我创建对象的时候虚拟机无法分配新的内存的时候，就会出现OutOfMemoryError

4. jvm 中一次完整的 GC 流程是怎样的，对象如何晋升到老年代，说说你知道的几种主要的jvm 参数。
> 老年代和新生代的比例是2:1。
>新生代中Eden和survivor的比例是8:1:1。
> 新建一个对象，如果新生代的内存空间不足，会触发一次MinorGC，采用复制算法，把 eden 区和 survivor1 区存活的拷贝到 survivor2 区，其余的都清除。当存活的年龄超过15，会放进老年代。如果对象很大会直接进入老年代。如果老年代内存不够会触发MajorGC，使用标记整理。

5. JVM调优
> -Xms初始 Heap 内存； -Xmx 最大 Heap 内存； -Xmn 年轻代大小；-Xss 每个线程的堆栈大小；-XX:MaxTenuringThreshold 转为老年代的年龄
> -XX:+HeapDumpOnOutOfMemoryError, -XX:HeapDumpPath 当发生内存溢出的时候dump出内存快照便于检查。
> -XX:+PrintGCTimeStamps 打印gc， -Xloggc:filename gc的文件名 
> 可以配置在内存溢出的时候执行的脚本，比如发邮件之类。 
> 可以配置代码缓存，当代码缓存满的时候字节码不会在被编译成机器码，导致运行速度降级。

6. 如何确定垃圾
> 引用技术法，问题是循环引用的话没有办法回收
> 可达性分析法，以GC roots对象作为起点搜索，如果没有可达路径就称为不可达对象。不可达对象变成可回收对象至少经过两次标记。
> 可以作为GC root对象的类型：java stack和native method statck中引用的对象，以及Method Area的Runtime Constant Pool中的引用对象

7. 垃圾回收算法
> 标记清除： 碎片化严重
> 复制： 为了解决标记清除的碎片化问题。年轻代正在用，只不过原本的算法是把内存分成1:1，现在的HotSpot是分成8:1:1，因为前者导致可能内存被压缩到了一半。
> 标记整理： 解决上面两个算法的缺陷而设计的。也是老年代使用的算法。
> 分代收集，老年代回收的少，年轻代回收的多，针对不同的特性选择各自更适合的回收算法
> 分区收集，将整个Heap分成一个一个小区间，每个区间独立回收。这样可以控制一次回收多少个区间，来合理的回收而不是整个堆。

8. 垃圾收集器
> Serial 使用复制算法以及使用单线程，当gc时其他工作线程要暂停工作。是VM在client模式下的默认新生代收集器。
> ParNew 是Serial的多线程版，也是复制算法，gc时其他工作线程同样要暂停工作。
> Parallel Scavenge 只是比 ParNew 多了自适应调节策略来更加高效的使用CPU。也是针对新生代的收集器。是VM在server模式下的默认新生代收集器。
> Serial Old 和Serial一样使用单线程，当gc是其他工作线程要暂停工作。不同的是使用标记整理算法。是VM在client模式下的默认老生代收集器。
> Parallel Old 是Parallel Scavenge的老年代版本，除了算法用标记整理以外，和它一模一样。
> CMS 使用标记清除算法。比前几个要复杂一点，先是初始标记(标记和gcroot直接关联的对象，暂停其他线程)，然后并发标记(追踪gcroot，不用暂停其他线程)，接着重新标记(修正并发标记期间发生变动的对象，暂停其他线程)，最后并发清除(不暂停其他线程)。主要目的是获得最短的停顿时间。
> g1 流程和CMS差不多，但是使用标记整理算法和分区收集。不会产生碎片以及效率高。
> JDK1.8在server模式下默认是 Parallel Scavenge + Serial Old 。1.9之后默认是G1并且废弃CMS。

9. 垃圾回收的实现原理
> 首先是如何确定垃圾，然后是垃圾回收的算法，最后讲垃圾收集器。
> System.gc() 只能是发送了一个gc的请求，至于会不会执行，什么时候执行是不能确定的。

10. 内存泄漏
> 根据泄漏的类型排查问题
> 老年代被占满，代码导致长期无法释放对象，或者年轻代的survivor过小导致minorGC把对象都放在老生代。
> 堆栈溢出，迭代或者递归没有退出

11. 类加载过程
> 装载 :在内存中生成这个对象
> 验证 :确定Class文件的信息是否符合虚拟机的要求
> 准备： 为类变量分配内存和初始值，即在方法区中分配变量所使用的内存空间。如果是public static final int v = 8080;初始值是8080.如果没有final修饰就是0。
> 解析: 将常量池中的符号引用转为直接引用。符号引用的目标不一定已经加载到内存中，但是直接引用的目标肯定是在内存中的。
> 初始化: 静态变量和静态代码块，成员变量和构造函数
> 最终产品是在Heap中的class对象，封装了类在方法区中的数据结构，并提供访问数据结构的接口。

12. 类加载器
> JVM默认的加载顺序是 BootStrapClassLoader -> ExtClassLoader -> AppClassLoader -> CustomClassLoader 

13. ClassLoader.loadClass和 Class.forName 的区别
> ClassLoader.loadClass不会初始化类,  Class.forname 会初始化类以及静态代码块

14. JVM的栈中引用如何和堆中的对象产生关联
> 对象生成时，产生的首地址存储在栈中，这样两者就关联起来了。

15. 逃逸分析
> 通过逃逸分析决定是否需要把对象放在heap上，而直接放在java stack里，作为一个栈帧的一部分。

## Mysql
1. Mysql 的InnoDB引擎为什么使用B+树的索引模型
> 与红黑树相比，B+是多路树，红黑树是二叉树，这样红黑树的高度会更大，也就是io的次数会更多，所以不适合
> 与B-树相比，B+树只在叶子节点存放数据，而B-在每个节点都存放数据。没有数据存储这样每一页的key就比B+多，高度更浅，io次数更少。而且B+的所有叶子节点有链接，这样就可以范围查找和分页查找。
> 与Hash相比，可以范围查找，可以排序，最左前缀匹配规则。但是hash的等值查找很快。

2. B+树的叶子节点存什么
>  如果是主键索引存的就是整行数据，如果是非主键索引存的就是主键的值，如果覆盖索引的话就不需要回表查询。

3. Mysql的隔离级别
> 我们使用的mysql级别是5.5
> 我们线上的级别是可重复读(Repeatable Read)也是mysql的默认级别，第二高的级别。读的时候上锁，但是无法锁住插入的数据，导致多了一个数据，这就是幻读。
> 重复读就是第一次读和第二次读的相同的sql但是结果不一样，所以使用重复读的隔离界别，读的时候上锁这样就不会出现前面的情况。
> Read Uncommitted < Read Committed < Repeatable Read < Serializable

4. MySQL5.6中对索引做了哪些优化
> 索引下推技术，例如有索引（key1，key2）,SQL语句中where key1 = 'XXX' and key2 like '%XXX%'，如果没有使用索引下推技术，MySQL会通过key1 = 'XXX'从存储引擎返回对应的数据至MySQL服务端，服务端再基于key2 like 判断是否符合条件。如果使用了索引下推技术，MySQL首先返回key1='XXX'的索引，再根据key2 like 判断索引是否符合条件，如果符合则通过索引定位数据，如果不符合则直接reject掉。有了索引下推优化，可以在有like条件查询的情况下，减少回表次数。

5. 什么情况下会发生明明创建了索引，但是执行的时候并没有通过索引呢
> sql执行前查询优化器会找出执行该sql的所有方案，然后找出成本最低的方案执行。
> 找出所有可能使用的索引->计算全表查询的代价->计算不同索引的代价->选择成本最低的实行

6. MySQL一条查询语句是如何执行的
> 连接器->查询缓存->分析器->优化器->执行器

7. MySQL是如何做到可以恢复到任意一秒状态的
> 有两个日志,redo和bin，这两个是保持一致的。Mysql是两阶段提交，写入redo并prepare->写入bin->commit

8. Mysql有哪些引擎
> MyISAM 插入查询快，但是不支持事物
> csv，每个表创建一个csv文件

9. 事务特性ACID
> Atomicity 原子性，要么都成功要么都失败
> Consistency 一致性，数据保持一致。比如转账，两个帐户总金额在转账后不能变。
> Isolation 隔离性，并发的AB事务，A事务来看B要么已经做好，要么还没做。
> Durability 持久化，修改永久保持

10. 慢查询
> show status可以看Slow_queries
> explain来看是否使用了index，possible_keys可能使用的索引，key实际使用的key

11. MVCC
> 普通的select都是快照读，下面的是当前读
> 读的的时候不能写(S锁) select * from table where ? lock in share mode;
> 写的时候不能读(X锁) select * from table where ? for update; 


## 算法
1. 给定一个整数数组 nums 和一个目标值 target，请你在该数组中找出和为目标值的那 两个 整数，并返回他们的数组下标。
> 使用Map保存'值-索引'的方式可以遍历一次完成效果，时间复杂度O(n)，空间复杂度O(n)。

2. 二叉树遍历算法
> 先序遍历：先根、后左、再右
> 中序遍历：先左、后根、再右
> 后序遍历：先左、后右、再根
```java
  public static class TreeNode {
    int val;
    TreeNode left;
    TreeNode right;
  }
  public static void preOder(TreeNode root, ArrayList<Integer> result) {
    if (root == null) {
      return;
    }
    result.add(root.val);
    if (root.left != null) {
      preOder(root.left, result);
    }
    if (root.right != null) {
      preOder(root.right, result);
    }
  }
```

3. 已知 sqrt (2)约等于 1.414，要求不用数学库，求 sqrt (2)精确到小数点后 10 位
> 使用二分法进行迭代,退出条件式两数只差小于小数点后 10 位

4. 给定一个二叉搜索树(BST)，找到树中第K小的节点
> BST的重要性质，中序遍历是有序数组。这样通过中序遍历并以K作为倒计时，K=0时即可结束遍历。

5. 给定一个链表，删除链表的倒数第 n 个节点，并且返回链表的头结点。
> 使用快慢指针，让快指针比慢指针多走n个节点，然后一起往后走，当快指针走到头的时候，慢指针后面的节点就是要被删除的节点。

6. 二分查找
> 首先是要有序的数组，然后循环去用start和end的中间的数与目标数进行比较，然后缩小范围。
```java
  private static int search(int[] arr, int search) {
    Arrays.sort(arr);
    int start = 0, end = arr.length - 1;
    while (start <= end) {
      int mid = (start + end) / 2;
      if (arr[mid] == search) {
        return mid;
      } else if (arr[mid] < search) {
        start = mid + 1;
      } else {
        end = mid - 1;
      }
    }
    return -1;
  }
```
7. 选择排序
> 每一轮都遍历一遍找出最小的数字，并和前面的数进行调换
```java
  private static int[] selectSort(int[] arr) {
    for (int i = 0; i < arr.length; i++) {
      int min = arr[i];
      int position = i;
      for (int j = i + 1; j < arr.length; j++) {
        if (arr[j] < min) {
          min = arr[j];
          position = j;
        }
      }
      arr[position] = arr[i];
      arr[i] = min;
    }
    return arr;
  }
```
8. 冒泡排序
> 每一轮都把最大的以冒泡的方式排到最后
```java
  private static int[] bubble(int[] arr) {
    for (int i = 0; i < arr.length; i++) {
      for (int j = 0; j < arr.length - i - 1; j++) {
        if (arr[j] > arr[j + 1]) {
          int temp = arr[j];
          arr[j] = arr[j + 1];
          arr[j + 1] = temp;
        }
      }
    }
    return arr;
  }
```
9. 插入排序
> 模拟打扑克牌，从第二个数字开始，与前面的数字进行比较，插入的时候后面的数字都依次往后移动一位。
```java
  private static int[] insertSort(int[] arr) {
    for (int i = 1; i < arr.length; i++) {
      int j = i, insert = arr[i];
      while (j - 1 >= 0 && insert < arr[j - 1]) {
        arr[j] = arr[j - 1];
        j--;
      }
      arr[j] = insert;
    }
    return arr;
  }
```
10. 希尔排序
> 是插入排序的高级版，插入排序是依次与前面有序数组进行比较，而希尔是设置了步长，第一次的步长是数组长度的一半，每做完一轮就再除以2，直到最后步长是1，也就是退化成插入排序。
```java
  private static int[] shellSort(int[] arr) {
    for (int k = arr.length / 2; k > 0; k /= 2) {
      for (int i = k; i < arr.length; i++) {
        int j = i, insert = arr[i];
        while (j - k >= 0 && insert < arr[j - k]) {
          arr[j] = arr[j - k];
          j = j - k;
        }
        arr[j] = insert;
      }
    }
    return arr;
  }
```
11. 快速排序
> 使用迭代的算法，取一个base值，第一次就取数组第一个数作为base，j 指针从后往前找第一个比base小的然后不再移动，接着 i 指针从前往后找第一个比base大的，然后 i 和 j 指针的值互换，不停循环直到两个指针的位置一样。这时再以这个位置为分隔，每个区间自己重复上面的排序，直到区间无法再分为止。
```java
  private static int[] quickSort(int[] arr, int start, int end) {
    if (start > end) {
      return arr;
    }
    int i = start, j = end, base = arr[start];
    while (i < j) {
      while (base <= arr[j] && i < j) {
        j--;
      }
      while (base >= arr[i] && i < j) {
        i++;
      }
      if (i < j) {
        int temp = arr[j];
        arr[j] = arr[i];
        arr[i] = temp;
      }
    }
    arr[start] = arr[i];
    arr[i] = base;
    quickSort(arr, start, j - 1);
    quickSort(arr, j + 1, end);
    return arr;
  }
```
12. 归并排序
> 使用迭代的算法，把数组无限的分开，直到每个区间只有一个数，然后再反过来一层一层的进行排序合并，最后拼成完整的数组。
```java
  private static int[] mergeSort(int[] a, int low, int high) {
    int mid = (low + high) / 2;
    if (low < high) {
      mergeSort(a, low, mid);
      mergeSort(a, mid + 1, high);
      merge(a, low, mid, high);
    }
    return a;
  }

  private static void merge(int[] arr, int start, int mid, int end) {
    int[] temp = new int[end - start + 1];
    int i = start, j = mid + 1, k = 0;
    while (i <= mid && j <= end) {
      if (arr[i] < arr[j]) {
        temp[k++] = arr[i++];
      } else {
        temp[k++] = arr[j++];
      }
    }
    while (i <= mid) {
      temp[k++] = arr[i++];
    }
    while (j <= end) {
      temp[k++] = arr[j++];
    }
    for (int i1 = 0; i1 < temp.length; i1++) {
      arr[start + i1] = temp[i1];
    }
  }
```
13. 基数排序
> 针对数字比较大的数组排序，使用递归的方式。首先取出数组中最大的数，根据他的位数决定递归几次。比如第一次迭代就是第一位数字进行比较，用一个Map<Integer,List<>> 保存这一位出现的数字，以及对应的数。遍历完之后从0-9的顺序取出map中的list，依次覆盖原数组。然后进行下一轮迭代。
```java
  private static int[] radixSort(int[] arr) {
    int max = 0;
    for (int i = 0; i < arr.length; i++) {
      if (arr[i] > max) {
        max = arr[i];
      }
    }
    int times = 0;
    while (max > 0) {
      max = max / 10;
      times++;
    }
    Map<Integer, List<Integer>> map = new HashMap<>();
    for (int i = 0; i < times; i++) {
      for (int j = 0; j < arr.length; j++) {
        int x = arr[j] % (int) Math.pow(10, i + 1) / (int) Math.pow(10, i);
        map.computeIfAbsent(x, v -> new ArrayList<>()).add(arr[j]);
      }
      int count = 0;
      for (int j = 0; j < 10; j++) {
        while (map.containsKey(j) && map.get(j).size() > 0) {
          arr[count++] = map.get(j).remove(0);
        }
      }
    }
    return arr;
  }
```
14. 堆排序
> 参考  https://www.cnblogs.com/Java3y/p/8639937.html
> 堆就是完全二叉树。相对二叉树不存在数组空间浪费。
> 完全二叉树：除了最后一层之外的其他每一层都被完全填充，并且所有结点都保持向左对齐。
> 完全二叉树特性：左子节点index = 父节点的index的两倍 + 1，右子节点index = 父节点的index的两倍 + 2
> 通过循环的方式，先按照完全二叉树的模型建堆，然后从最后一个父节点开始比较其与子节点的大小关系，小的话就和较大的子节点进行对调，对调后的以子节点的位置做上面的比较和对调。依次遍历完所有父节点之后。建堆结束。这时最大值就在堆顶，然后把最后一个叶子节点和堆顶对调，进行下一轮无序数组的遍历。有点像冒泡算法，每一轮都是把最大值放到无序数组的后面。
```java
  private static int[] heapSort(int[] arr) {
    System.out.println(Arrays.toString(arr));
    int arrayLength = arr.length;
    for (int i = 0; i < arrayLength - 1; i++) {
      buildMaxHeap(arr, arrayLength - 1 - i);
      System.out.println("build" + Arrays.toString(arr));
      swap(arr, 0, arrayLength - 1 - i);
      System.out.println("swap" + Arrays.toString(arr));
    }
    return arr;
  }

  private static void swap(int[] data, int i, int j) {
    int tmp = data[i];
    data[i] = data[j];
    data[j] = tmp;
  }

  private static void buildMaxHeap(int[] data, int lastIndex) {
    for (int i = (lastIndex - 1) / 2; i >= 0; i--) {
      int k = i;
      while (k * 2 + 1 <= lastIndex) {
        int biggerIndex = 2 * k + 1;
        if (biggerIndex < lastIndex) {
          if (data[biggerIndex] < data[biggerIndex + 1]) {
            biggerIndex++;
          }
        }
        if (data[k] < data[biggerIndex]) {
          swap(data, k, biggerIndex);
          k = biggerIndex;
        } else {
          break;
        }
      }
    }
  }
```

## 多线程
1. 线程的实现方式
> 实现Runnable接口，重写run方法，调用Thread的构造函数。
> 继承Thread，重写run方法
> 通过线程池创建
> 通过 Callable 或者 FutureTask 创建线程

2. volatile关键字
> 一般线程修改值的流程是：从主内存中读取值到工作内存，修改工作内存的值，刷回主内存。
> 而被修饰的变量改变时会把其他线程的工作内存中的值失效，这样其他的线程不会从内存中读取值，而是重新从主内存中取。
> 只能保证可见性，不具备原子性，不能替代锁的作用。

3. Executors提供四种线程池方法
> newCachedThreadPool : corePoolSize设置为0，maximumPoolSize设置为Integer最大值，过期时间60秒，队列使用 SynchronousQueue。
> newFixedThreadPool(int nThreads) : corePoolSize设置为nThreads，maximumPoolSize设置为nThreads，没有过期时间，队列使用 LinkedBlockingQueue。
> newScheduledThreadPool(int corePoolSize) : corePoolSize设置为corePoolSize，maximumPoolSize设置为Integer最大值，无过期时间，队列使用 DelayedWorkQueue。
> newSingleThreadExecutor() : corePoolSize设置为1，maximumPoolSize设置为1，无过期时间，队列使用 LinkedBlockingQueue。
> 都是调用了ThreadPoolExecutor的构造函数，借助 ReentrantLock实现

4. BlockingQueue
> 队列中没有数据的时候消费端阻塞
> 队列中数据满了的时候生产端阻塞

5. 常见的BlockingQueue
> ArrayBlockingQueue：公平和非公平
> LinkedBlockingQueue：使用 ReentrantLock 的 condition 实现队列两端分别加锁。
> PriorityBlockingQueue：排序实现优先排序，可以让元素重写CompareTo或者给队列初始化的时候设置Comparator
> DelayQueue：延迟期满才能取元素，可以用来做缓存和定时任务
> SynchronousQueue：不存储数据, offer的时候如果没有take线程在等待的话，直接return false，(offer可以设置超时时间)。
> LinkedTransferQueue：立即被消费。相比其他队列多了transfer和tryTransfer的方法。transfer：如果有消费者在等待则直接给消费者，如果没有就放在队列的尾部，等到被消费了才返回。tryTransfer：如果有消费者就直接给，没有的话返回false。
> LinkedBlockingDeque：双向阻塞数组，可用于工作窃取模式。

6. 拒绝策略
> AbortPolicy: 是jdk的默认策略，直接抛出异常
> CallerRunsPolicy：在任务提交的线程上直接做任务
> DiscardOldestPolicy：丢弃最老的任务
> DiscardPolicy：丢弃当前任务

7. 线程池工作流程
> 线程池刚创建的时候是没有线程的，任务队列作为参数传进来，即使队列中有任务也不会执行。
> 当调用execute()的时候，如果运行线程小于 corePoolSize则创建新的。如果大于或等于corePoolSize则放入队列。队列满了的情况下如果运行线程比maxPoolSize小，就创建非核心线程，如果大于等于maxPoolSize就执行拒绝策略。
> 当线程做完了就去队列取下一个任务。
> 根据设置的过期时间，决定要不要关掉线程。

8. 线程的生命周期
> ![]( https://hsindumas.github.io/images/Thread.png )
> new: new Thread()
> runnable：thread.start()， jvm创建java stack和pc register
> running：获得到cpu就执行run()
> blocking：wait、锁 、sleep或者join，之后回复到runnable的状态
> dead：正常结束、异常结束、thread.stop()。stop()已经废弃，释放锁会导致不同步的问题，可以自定义标志去退出或者使用interrupt()，以及isInterrupt()去做控制

9. seelp和wait的区别
> sleep是Thread的静态方法，wait是实例化对象的内部方法
> sleep不会释放锁，wait会释放锁
> sleep是放到等待队列，而wait是放到锁池

10. 锁的种类
> 乐观锁： CAS
> 悲观锁:	Synchronized
> 自旋锁：如果持有锁的线程能很快的释放锁，那就不需要从running到blocking再到runnable，只要占用cpu等一等就可以了，省去线程的上下文切换。JDK1.7后有JVM自己控制

11. ReentrantLock 和 synchronized的区别
> ReentrantLock实现了Lock接口，依赖了AQS来实现，并通过lock()和unlock()进行控制，需要手动解锁。synchronize是由JVM控制自动解锁
> ReentrantLock可以中断、支持公平锁、以及多个锁(读写锁)。ReentrantReadWriteLock 读锁不互斥，写锁互斥，读的时候上读锁，写的时候上写锁。
> synchronized是通过对象内部的monitor实现，不可以中断。
> Lock有三个实现类，就是ReentrantLock , ReentrantReadWriteLock.ReadLock, ReentrantReadWriteLock.WriteLock。

12. 锁优化
> 减少锁持有时间。只有在必要的代码上加锁。
> 减小锁的颗粒度。比如ConcurrentHashMap的分段锁。
> 锁粗化。如果不停的对一个锁加锁解锁不如把锁粗一点
> 锁分离。比如ReentrantLock的读写锁，以及LinkedBlockingQueue。

13. AtomicInteger 底层实现
> 使用volatile修饰value，保证可见性。
> 使用CAS保证安全的修改值。
> getAndIncrement()方法其实是一个do while循环尝试用cas更新数据。

14. 什么是 AQS(Abstract Queue Synchronizer)
> ReentrantLock、Semaphore、CountDownLatch都依赖它。
> 两种资源共享方式：独占(ReentrantLock)和共享(Semaphore、CountDownLatch)
> ReentrantReadWriteLock 独占和共享都实现了。

15. 关闭线程池的方法
> shutdown: 停止submit->状态改成shutdown->已经sumbit的继续执行->执行完stop
> shutdownNow：返回未执行的task->状态改成stop->停止正在执行的任务

16. 假如有一个第三方接口，有很多个线程去调用获取数据，现在规定每秒钟最多有 10 个线程同时调用它，如何做到
> 因为Executors.newScheduledThreadPool，设置10个corePoolSize，把maxPoolSize也改成10，然后设置delay为一秒。

17. execute和submit区别
> execute只能提交Runnable，但是submit能提交Callable

18. 三个线程按顺序执行一次的实现方式 
```java
  private static Semaphore first = new Semaphore(0);
  private static Semaphore second = new Semaphore(0);

  public static void main(String[] args) {
    Thread thread1 =
        new Thread(
            () -> {
              System.out.println("1");
              first.release();
            });
    Thread thread2 =
        new Thread(
            () -> {
              try {
                first.acquire();
              } catch (InterruptedException e) {
                e.printStackTrace();
              }
              System.out.println("2");
              second.release();
            });
    Thread thread3 =
        new Thread(
            () -> {
              try {
                second.acquire();
              } catch (InterruptedException e) {
                e.printStackTrace();
              }
              System.out.println("3");
            });
    thread3.start();
    thread2.start();
    thread1.start();
  }
```
19. 三个线程按顺序执行多次的实现方式 
```java
  private static Semaphore first = new Semaphore(1);
  private static Semaphore second = new Semaphore(0);
  private static Semaphore third = new Semaphore(0);

  public static void main(String[] args) {
    Thread thread1 =
        new Thread(
            () -> {
              for (int i = 0; i < 10; i++) {
                try {
                  first.acquire();
                } catch (InterruptedException e) {
                  e.printStackTrace();
                }
                System.out.println("1");
                second.release();
              }
            });
    Thread thread2 =
        new Thread(
            () -> {
              for (int i = 0; i < 10; i++) {
                try {
                  second.acquire();
                } catch (InterruptedException e) {
                  e.printStackTrace();
                }
                System.out.println("2");
                third.release();
              }
            });
    Thread thread3 =
        new Thread(
            () -> {
              for (int i = 0; i < 10; i++) {
                try {
                  third.acquire();
                } catch (InterruptedException e) {
                  e.printStackTrace();
                }
                System.out.println("3");
                first.release();
              }
            });
    thread3.start();
    thread2.start();
    thread1.start();
  }
```
20. ThreadLocal原理
> 每个Thread内部都有一个 ThreadLocal.ThreadLocalMap
> ThreadLocalMap是ThreadLocal内部类，没有实现Map接口，是自己实现Map的功能
> 一个ThreadLocal只能保存一个副本，想要多个就创建多个ThreadLocal。
> 使用场景session

21. 实现一个并发安全的链表
> Collections.synchronizedList()会根据instanceof RandomAccess来区别数组还是链表

22. CountDownLatch , cyclicbarrier,
> CountDownLatch一般用于某个线程A等待若干个其他线程执行完任务之后，它才执行
> CyclicBarrier一般用于一组线程互相等待至某个状态，然后这一组线程再同时执行
> CountDownLatch是不能够重用的，而CyclicBarrier是可以重用的。
> Semaphore 其实和锁有点类似，它一般用于控制对某组资源的访问权限。

23. 使用 synchronized 修饰静态方法和非静态方法有什么区别。
> 修饰静态方法是对整个类加锁，非静态方法只是针对方法。

24. 死锁的必要条件
> 互斥条件：一个资源一次只能被一个线程使用
> 请求与保持条件：一个进程因请求资源而阻塞时，对已获得的资源保持不放
> 不剥夺权利：进程已获得资源后，在使用完前，不能被强行剥夺。
> 循环等待条件：若干资源形成首位相接的循环等待资源关系。

25. Callable和Runnable的区别如下：
> Callable定义的方法是call，而Runnable定义的方法是run。
> Callable的call方法可以有返回值，而Runnable的run方法不能有返回值。
> Callable的call方法可抛出异常，而Runnable的run方法不能抛出异常。

## http
1. http1.0，http1.1，http2.0
> 1.1增加了长链接
> 2.0采用二进制格式，1.x采用文本的格式
> 1.0一个请求一个链接，1.1一次链接发多个请求，但是是串行的，会有阻塞的情况，2.0采用多路复用，解决1.1的阻塞问题。

2. TCP 三次握手
> ![]( https://hsindumas.github.io/images/http-establish.png )
> 第三次握手确保，客户端不会因为网络问题，没有收到第二次握手的信息，而导致链接并没有建立成功。

3. TCP 四次挥手
> ![]( https://hsindumas.github.io/images/http-close.png )
> TIME_WAIT状态就是用来重发可能丢失的ACK报文
> TIME_WAIT是主动关闭，CLOSE_WAIT是被动关闭

4. TCP和HTTP
> TCP是底层通信协议，规定数据传输和链接的方式
> HTTP是应用层协议，规定数据内容的方式

5. https
> 客户端发给请求给服务器，服务器返回证书，包含公钥。然后客户端校验后生成客户端密钥，并用公钥加密再传给服务器，服务器用服务器密钥解密后获得客户端密钥，用客户端密钥加密传输的数据，客户端拿到数据去解密。


## io
1. io的理解
> IO，使用多路复用，但是需要轮询是否有IO操作，基于字节流和字符流。使用select/poll
> NIO，三大核心Channel、Buffer、Selector。Selector管理多个信道，当有信道需要IO操作的时候，会通知Selector。基于Channel、Buffer。使用epoll。
> AIO相对于NIO来说，不需要程序自己赋值数据到用户空间，以及通过设置回调函数的方式而不用程序自己调用处理逻辑。

2. epoll和select区别
> epoll 是一种信号通知的被动机制，而select是需要主动轮询。

3. Java IO流的层次结构
> 字节流是InputStream,OutputStream
> 字符流是Writer,Reader

4. FileInputStream在使用完以后，不关闭流，想二次使用可以怎么操作
> 使用反射的方式，首先判断inputStream有没有读完，当读完时，利用反射去调内部open方法去第二次打开文件。 

5. Reactor模型
> Reactor单线程模型:
> ![]( https://hsindumas.github.io/images/reactor单线程模型.png )
> Reactor多线程模型:
> ![]( https://hsindumas.github.io/images/reactor多线程模型.png )
> 主从Reactor多线程模型:
> ![]( https://hsindumas.github.io/images/主从reactor多线程模型.png )


## 设计
1. 分布式集群下如何做到唯一序列号
> mysql自增，也可以设置步长。
> redis利用单线程的特性，可以和mysql一样设置步长
> 使用uuid，排序效率低
> mongodb使用的objectid算法

2. 设计一个秒杀系统
> 分流，再牛逼的机器也抵挡不住高级别的并发
> 限流，秒杀商品有限，防刷的前提下没有绝对的公平，比如guava的RateLimiter进行限流，设置每秒多少个令牌。
> 缓存，尽量不要让大量请求穿透到DB层，活动开始前商品信息可以推送至分布式缓存
> 异步，分析并识别出可以异步处理的逻辑，比如日志，缩短系统响应时间

3. redis实现队列
> 用List去实现，RPUSH，LPOP 尾部插入，头部取出。行程队列的模式

4. redis实现分布式锁
> setnx，只有key不存在的情况下才能set成功。
> 为了避免线程挂掉没有del去释放锁，从而造成的死锁，建议给key设置一个过期时间。
> 极端情况expire还没设置线程就挂了，从而造成死锁，使用redis新的api ，set的时候key，value，过期时间，以及是不是nx都是可以设置的。
> 极端情况，过期时间小于任务完成的时间，导致真正删除的时候，并不是自己获得的锁。可以把唯一标识放进value中，del之前先校验。但是这种情况下仍然会有两个线程同时进入代码块，想办法重新设置过期时间来给锁续命。

5. zookeeper实现分布式锁
> 多个jvm再同一个节点下创建临时有序节点，序号最小的先获得锁，等任务结束删除了节点zookeeper会通知，被listener监听到后序号最小的获得锁。

6. 分布式锁redis和zookeeper比较
> redis性能高，小心死锁
> zookeeper 可以重入，阻塞锁，可以释放锁。创建临时节点，如果客户端退出，节点也会被删除，相当于锁释放。

7. 高并发框架设计
> 分层，应用层-服务层-数据层
> 分割，按照业务功能分 

8. 设计和实现一个LRU（最近最少使用）缓存数据结构
> 直接继承LinkedHashMap，调用super的构造函数，并设置以访问的顺序排序，重写removeEldestEntry方法，如果大于容量就返回true，如果要求没有的话返回-1就需要重写get方法。
```java
class LRUCache extends LinkedHashMap<Integer, Integer>{
    
    public LRUCache(int capacity) {
        super(capacity, 0.75F, true);
    }

	@Override
    public int get(int key) {
        return super.getOrDefault(key, -1);
    }

    @Override
    protected boolean removeEldestEntry(Map.Entry<Integer, Integer> eldest) {
        return size() > capacity; 
    }
}
```

9. 自己动手写一个简单的阻塞队列
> 利用ReentrantLock进行加锁，以及使用ArrayList(add,get)或者LinkedList(add,pop)实现队列
```java
public class SimpleBlockingQueue {
  private List<Integer> container = new ArrayList<>();
  private int count;
  private Lock lock = new ReentrantLock();
  private final Condition conditionNull = lock.newCondition();
  private final Condition conditionFull = lock.newCondition();

  private void add(Integer item) throws InterruptedException {
    if (item == null) {
      throw new NullPointerException();
    }
    lock.lockInterruptibly();
    try {
      System.out.println("添加元素:" + item);
      ++count;
      container.add(item);
      System.out.println("唤醒阻塞线程...");
      conditionNull.signal();
      Thread.sleep(2000);
    } finally {
      System.out.println("添加方法释放锁...");
      // 手动释放锁
      lock.unlock();
    }
  }

  private Integer take() throws InterruptedException {
    lock.lockInterruptibly();
    try {
      try {
        while (count == 0) {
          System.out.println("队列元素为空，进入阻塞...");
          conditionNull.await();
        }
      } catch (InterruptedException ie) {
        System.out.println("出现异常，唤醒阻塞线程conditionNull");
        conditionNull.signal();
        throw ie;
      }

      --count;
      Integer x = container.get(0);
      System.out.println("取出方法取出元素:" + x);
      conditionFull.signal();
      return x;

    } finally {
      System.out.println("取出方法释放锁...");
      lock.unlock();
    }
  }
}
```
10. 分布式系统统一登录模块设计
> 使用redis存储session
> spring session可以配置 store-type 为redis

## dobbo
1. Dubbo的原理
> Doubbo使用的rpc协议，Spring Cloud使用的是rest协议
> 客户端通过注册发现获取provider的地址，然后根据策略去选择一个provider发送请求。请求封装好的一个对象，里面设置了具体的方法以及参数之类，服务端根据接收的对象去做相应的处理。
> 快速失败的策略是只发起一次调用，失败立即报错。

2. RPC 请求的流程是什么
> 服务消费方（client）调用以本地调用方式调用服务
> client stub接收到调用后负责将方法、参数等组装成能够进行网络传输的消息体
> client stub找到服务地址，并将消息发送到服务端
> server stub收到消息后进行解码
> server stub根据解码结果调用本地的服务
> 本地服务执行并将结果返回给server stub
> server stub将返回结果打包成消息并发送至消费方
> client stub接收到消息，并进行解码
> 服务消费方得到最终结果

3. 编程中自己都怎么考虑一些设计原则的
> 类包括接口类尽量单一功能，减少耦合。

## mybatis
1. mybatis原理

2. MyBatis缓存
> 一级缓存是sqlSession级，sqlSession之间不共享
> 二级缓存可以共享

## mq
1. RocketMQ
> 公司现在使用的是RocketMQ，但是我到公司的时候就已经运行了。有问过为什么选这个，老员工说是用JAVA写的，有问题可以自己看代码。
> 公司主要用来发送客户端的推送，以及一些统计日志。
> 生产者设置一个topic，然后发送给mq，消费者起监听去获取这个topic的任务，然后拿来消费。

2. 怎么实现顺序消费
> 参考 https://www.cnblogs.com/telwanggs/p/10737524.html
> 比如两个任务需要按照顺序执行，如果发送到不同的mqserver是没办法控制顺序的，但是按照顺序发送到同一个mqserver也是有问题的，会被不一样的消费者消费，而这个顺序是不可控的。最终的解决方案是取一个唯一id，两个任务都传这个id给mqserver，mqserver在选择消费者的时候用id和消费者数量取余数，这样就限制了相同id的任务只会被同一个消费者消费，从而达到顺序消费的效果。

3. 消息重复的解决办法
> 幂等性，消息多次消费跟只消费一次的效果要一样
> 使用日志表，用id标识，做过的任务忽略

4. 事务
> 一般使用Spring的事务解决。RocketMQ的事务如果消费端出现了问题，mq是不会处理的，只能自己回滚，反而会比较麻烦。

## redis
1. redis的list操作
> LPUSH,RPUSH,LPOP,RPOP,LLEN,LRANGE

2. redis的数据结构
> string,hash, list, set, sortSet(排名:zadd,zscore,zrevrange)

3. 淘汰策略
> noeviction:  当内存使用达到阈值的时候，所有引起申请内存的命令会报错。
> allkeys-lru:在主键空间中，优先移除最近未使用的key。
> volatile-lru：在设置了过期时间的键空间中，优先移除最近未使用的key。
> allkeys-random：在主键空间中，随机移除某个key。
> volatile-random：在设置了过期时间的键空间中，随机移除某个key。
> volatile-ttl：在设置了过期时间的键空间中，具有更早过期时间的key优先移除。

4. redis和memcached的区别
> redis支持的数据结构更多，速度更快，而且可以持久化数据

5. redis是单线程的么，所有的工作都是单线程么
> 不是，只是使用了一个线程处理网络请求，其他模块仍然是多个线程

## es
1. elasticsearch 了解多少
> 是一个分布式的搜索服务器，支持rest api。
> 我们公司目前业务中的用户搜索和高校搜索是使用es的，搜索的时候也可以根据经纬度进行排序。es的版本升级正好是我做的，线上用的7.2.0版本，我是搭建了四台服务，用户数据和高校数据通过logstash定时从mysql导入es。索引是两个一个user,一个school。user是有将近400万的数据，1.3G。一个主分片一个次分片。