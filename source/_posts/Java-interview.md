---
title: Java Interview
date: 2018-11-20 07:10:28
tags: [interview,java]
categories: interview
---

# Crash Java

Java研发工程师

<!-- more -->

### 海量URL去重

利用**布隆过滤器**代替哈希表，利用多Hash函数映射Bit Array的思想，可以减少内存的使用。缺点是有一定概率的假正率。

### 快排

```java
void quickSort(int[] s, int l, int r){
    if (l < r){
        int i = l, j = r, x = s[l];
        while (i < j){
            while(i < j && s[j] >= x) // 从右向左找第一个小于x的数
				j--;  
            if(i < j) 
				s[i++] = s[j];
			
            while(i < j && s[i] < x) // 从左向右找第一个大于等于x的数
				i++;  
            if(i < j) 
				s[j--] = s[i];
        }
        s[i] = x;
        quickSort(s, l, i - 1); // 递归调用 
        quickSort(s, i + 1, r);
    }
}

```

### GC ROOTS

虚拟机栈中引用的对象、方法区中类静态属性引用的对象、方法区中常量引用的对象、本地方法栈中引用的对象

### 负载均衡算法

轮询、随机、最小连接、源地址一致性哈希。考虑到服务器处理能力的不同，可以采用加权的策略。

### 一致性哈希

用来解决分布式缓存的Hot Spot问题。将对象与服务器映射到哈希环上，离对象顺时针最近的就是存储的服务器，增删节点只会影响一小段哈希环上的对象。还要注意一下为了解决环偏斜可以引入虚拟节点。

### 数据库分库

垂直拆分：根据业务将表按字段拆分。优点：业务清晰、系统更简洁、维护简单。缺点：单库无法Join，事务复杂，性能有可能受影响。

水平拆分：数据行拆分。

### 服务发布消费过程

>  主要考察Dubbo源码分析，还不了解。

### 分布式锁

数据库乐观锁实现方式：加时间戳或版本号。

Redis实现悲观锁，要注意解锁的时候要使用事务，如Lua脚本。乐观锁可以借助WATCH命令实现CAS操作。

### 自旋锁、锁消除、锁粗化、轻量级锁、偏向锁

都是一些JVM自带的锁优化策略。

自旋锁：自旋等待要占用CPU时间，但是可以避免线程切换的开销，在锁被占用时间很短的情况下效果很好。自旋锁有自旋次数参数，超过这个次数就会挂起线程。此外，还有自适应自旋锁，可以根据上一次锁的自旋次数以及锁的当前拥有者状态来动态调整自旋次数。

锁消除：利用逃逸分析判断数据不会逃逸，被其他线程访问到。这时可以判定数据是线程私有，不需要锁同步。

锁粗化：大部分情形下，总是推荐同步代码块尽量小。但是也有例外的情形，比如一系列操作连续对同一个对象反复加解锁，这时可以使用锁粗化来避免不必要的互斥同步操作。

轻量级锁：在没有竞争的情况下，用CAS操作去修改对象头的Mark Word，标记轻量状态。如果有竞争情况，则膨胀为重量锁。

偏向锁：在锁第一次被线程获取时，会先设置成偏向，如果接下来没有被其他线程获取，则持有偏向锁的线程永远不需要进行同步。当有其他线程尝试获取锁时，偏向模式就结束了。

### Arrays.sort() & Collections.sort()

查看了Java8的源码。Collections.sort最终会用到Arrays的排序方法。

Arrays的排序方法：当排序元素量小于286、大于47时，使用双轴快排（DualPivotQuickSort），在小数组上（小于47），会使用插入排序。当排序元素量大等于286且有序性好的时候用归并排序（其中归并排序还有传统的归并排序和TimSort，传统归并上面还有注释说“以后会移除”），有序性不好时还是用双轴快排。

### 动态代理

在Spring中有JDK、CGLib两种方式实现动态代理。JDK方式适合有接口声明的类，CGLib适合没有接口声明的类。

JDK：通过反射机制来实现，并且效率比较慢。

CGLib：字节码增强技术是一种高效的动态代理方法，可以在内存中直接生成代理对象。CGLib底层使用了ASM字节码框架。主要实现原理还是通过持有被代理的对象，对被代理对象的方法进行拦截。CGLib是通过生成子类的方式覆盖指定类的方法，所以final方法无法拦截。

### Redis、ZooKeeper节点宕机

Redis：如果master宕机，并且该master没有slave，则集群宕机；如果超过半数master宕机，则无论是否有slave，集群宕机。Redis集群采用主从备份模式，主从节点会采用异步的方式进行同步。

ZooKeeper：有且仅有一个leader节点，负责保证数据的强一致性。只要集群的可用节点个数大于N/2+1就能提供服务。

### 分布式事务

XA协议、两阶段提交（2PC）、三阶段提交（3PC）、TCC（try、confirm、cancel）实现、MySQL XA实现

这个话题比较大，另外开一个post记述。

### MySQL事务隔离

四个隔离级别，从低到高：读未提交数据（Read uncommitted）、读已提交数据（Read committed）、可重复读（Repeatable read）、串行化（Serializable ）

分别对应的风险：脏读、不可重复读、幻读。直到串行化才没有以上风险。

幻读：一个事务修改全部数据行，另一个事务在同一个表中插入新数据，发现新插入的数据没有被修改。

一些实现细节：InnoDB的默认隔离级别-可重复读，其中用了Next-Key Lock去锁行，即表的间隙也不允许插入，这样可以有效防止幻读。

### AQS原理

CLH队列

volatile state以及FIFO线程等待队列，state重入次数，0表示未占用

两种资源共享方式：独占（ReentrantLock）、共享（Semaphore、CountDownLatch）

### JAVA8 ConcurrentHashMap.size()

几个关键方法与变量：sunCount()、baseCount、CounterCell[] counterCells、addCount()、fullAddCount()

### 消息丢失与重复消费

客户端可以采用消息确认机制（message acknowledgment），服务器端宕机可以采用持久化机制。重复消费可以通过接口幂等来解决（比如给消息加版本）

### Docker常用命令

docker run/start/exec/create/kill/rm、start/stop/restart、pause/unpause、top/logs/ps

还有一些镜像操作的命令，不太熟。

### HTTP相关

单工、无状态协议。全双工采用websocket。

SSL/TLS 主要安全手段：加密与证书。细节有空详谈。

### 永久代（PermGen）与元空间（Metaspace）

永久代是HotSpot上的概念，可以理解为方法区的实现。在Java8中已经取消了永久代，改为元空间。永久代是堆内存的一部分，元空间存储类的元数据并使用本地内存，并将永久代中的字符串常量池与类静态变量等放到堆中。

**原因** 一方面是可以减少OOM，最主要的原因是与JRockit虚拟机没有永久代概念，为了融合HotSpot与JRockit。