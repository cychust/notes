# Android面试准备

## 为什么静态变量在类创建之前就可以访问到

被static修饰的成员变量和成员方法独立于该类的任何对象。也就是说，它不依赖类特定的实例，被类的所有实例共享。只要这个类被加载，Java虚拟机就能根据类名在`运行时数据区的方法区`内定找到他们。因此，static对象可以在它的任何对象创建之前访问，无需引用任何对象。静态变量属于`类的属性`



## Error和Exception的联系

### Error和Exception的联系

继承结构：Error和Exception都是继承于Throwable，RuntimeException继承自Exception。

Error和RuntimeException及其子类称为未检查异常（Unchecked exception），其它异常成为受检查异常（Checked Exception）。

### Error和Exception的区别

Error类一般是指与虚拟机相关的问题，如系统崩溃，虚拟机错误，内存空间不足，方法调用栈溢出等。如java.lang.StackOverFlowError和Java.lang.OutOfMemoryError。对于这类错误，Java编译器不去检查他们。对于这类错误的导致的应用程序中断，仅靠程序本身无法恢复和预防，遇到这样的错误，建议让程序终止。

Exception类表示程序可以处理的异常，可以捕获且可能恢复。遇到这类异常，应该尽可能处理异常，使程序恢复运行，而不应该随意终止异常。

### 运行时异常和受检查的异常

Exception又分为运行时异常（Runtime Exception）和受检查的异常(Checked Exception )。

- RuntimeException：其特点是Java编译器不去检查它，也就是说，当程序中可能出现这类异常时，即使没有用try……catch捕获，也没有用throws抛出，还是会编译通过，如除数为零的ArithmeticException、错误的类型转换、数组越界访问和试图访问空指针等。处理RuntimeException的原则是：如果出现RuntimeException，那么一定是程序员的错误。

- 受检查的异常（IOException等）：这类异常如果没有try……catch也没有throws抛出，编译是通不过的。这类异常一般是外部错误，例如文件找不到、试图从文件尾后读取数据等，这并不是程序本身的错误，而是在应用环境中出现的外部错误。



### throw 和 throws两个关键字有什么不同

throw 是用来抛出任意异常的，你可以抛出任意 Throwable，包括自定义的异常类对象；throws总是出现在一个函数头中，用来标明该成员函数可能抛出的各种异常。如果方法抛出了异常，那么调用这个方法的时候就需要处理这个异常。





## 类加载过程



生命周期包括： 加载、验证、准备、解析、初始化、使用、卸载



> 在装载阶段，类装载器(Bootstrap ClassLoader 或者用户自定义的ClassLoader) 把编译形成的[class文件](https://www.baidu.com/s?wd=class%E6%96%87%E4%BB%B6&tn=44039180_cpr&fenlei=mv6quAkxTZn0IZRqIHckPjm4nH00T1Y4PHRdPj6Ym1TsnAPhrH6d0ZwV5Hcvrjm3rH6sPfKWUMw85HfYnjn4nH6sgvPsT6KdThsqpZwYTjCEQLGCpyw9Uz4Bmy-bIi4WUvYETgN-TLwGUv3EnHbvnjbvnHDdPW0vnj6sPHfvn0)载入内存，创建类相关的Class对象，这个Class对象封装了我们要使用的类的类型信息。
>
> 连接阶段又可以分为三个子步骤：验证、准备和解析。
> 验证就是要确保java类型数据格式 的正确性，并适于JVM使用。
> **准备阶段，JVM为静态变量分配内存空间，并设置默认值，注意，这里是设置默认值，比如说int型的变量会被赋予默认值0 。在这个阶段，JVM可能还会为一些数据结构分配内存，目的 是提高运行程序的性能，比如说方法表。**
> 解析过程就是在类型的常量池中寻找类、接口、字段和方法的符号引用，把这些符号引用替换成直接引用。这个阶段可以被推迟到初始化之后，当程序运行的过程中真正使用某个符号引用的时候 再去解析它。
>
> 类会在首次被“主动使用”时执行初始化，为类（静态）变量赋予正确的初始值。在Java代码中，一个正确的初始值是通过类变量初始化语句或者静态初始化块给出的。而我们这里所说的主动使用 包括：
> 1. 创建类的实例
> 2. 调用类的静态方法
> 3. 使用类的非常量静态字段
> 4. 调用Java API中的某些反射方法
> 5. 初始化某个类的子类
> 6. 含有main()方法的类启动时



## HTTP2.0 新特性

#### 二进制分帧

**帧：**HTTP/2 数据通信的最小单位消息：指 HTTP/2 中逻辑上的 HTTP 消息。例如请求和响应等，消息由一个或多个帧组成。

**流：**存在于连接中的一个虚拟通道。流可以承载双向消息，每个流都有一个唯一的整数ID。

HTTP/2 采用二进制格式传输数据，而非 HTTP 1.x 的文本格式，二进制协议解析起来更高效。 HTTP / 1 的请求和响应报文，都是由起始行，首部和实体正文（可选）组成，各部分之间以文本换行符分隔。HTTP/2 将请求和响应数据分割为更小的帧，并且它们采用二进制编码。

**HTTP/2 中，同域名下所有通信都在单个连接上完成，该连接可以承载任意数量的双向数据流。**每个数据流都以消息的形式发送，而消息又由一个或多个帧组成。多个帧之间可以乱序发送，根据帧首部的流标识可以重新组装。



#### 多路复用

多路复用，代替原来的序列和阻塞机制。所有就是请求的都是通过一个 TCP连接并发完成。 HTTP 1.x 中，如果想并发多个请求，必须使用多个 TCP 链接，且浏览器为了控制资源，还会对单个域名有 6-8个的TCP链接请求限制。

在 HTTP/2 中，有了二进制分帧之后，HTTP /2 不再依赖 TCP 链接去实现多流并行了，在 HTTP/2中：

- 同域名下所有通信都在单个连接上完成。
- 单个连接可以承载任意数量的双向数据流。
- 数据流以消息的形式发送，而消息又由一个或多个帧组成，多个帧之间可以乱序发送，因为根据帧首部的流标识可以重新组装。

这一特性，使性能有了极大提升：

- **同个域名只需要占用一个 TCP 连接**，消除了因多个 TCP 连接而带来的延时和内存消耗。
- 单个连接上可以并行交错的请求和响应，之间互不干扰。
- 在HTTP/2中，每个请求都可以带一个31bit的优先值，0表示最高优先级， 数值越大优先级越低。有了这个优先值，客户端和服务器就可以在处理不同的流时采取不同的策略，以最优的方式发送流、消息和帧。

#### 服务器推送

服务端可以在发送页面HTML时主动推送其它资源，而不用等到浏览器解析到相应位置，发起请求再响应。例如服务端可以主动把JS和CSS文件推送给客户端，而不需要客户端解析HTML时再发送这些请求。

服务端可以主动推送，客户端也有权利选择是否接收。如果服务端推送的资源已经被浏览器缓存过，浏览器可以通过发送RST_STREAM帧来拒收。主动推送也遵守同源策略，服务器不会随便推送第三方资源给客户端。



#### 头部压缩



HTTP 1.1请求的大小变得越来越大，有时甚至会大于TCP窗口的初始大小，因为它们需要等待带着ACK的响应回来以后才能继续被发送。HTTP/2对消息头采用HPACK（专为http/2头部设计的压缩格式）进行压缩传输，能够节省消息头占用的网络的流量。而HTTP/1.x每次请求，都会携带大量冗余头信息，浪费了很多带宽资源。

HTTP每一次通信都会携带一组头部，用于描述这次通信的的资源、浏览器属性、cookie等



为了减少这块的资源消耗并提升性能， **HTTP/2对这些首部采取了压缩策略**：

- HTTP/2在客户端和服务器端使用“首部表”来跟踪和存储之前发送的键－值对，对于相同的数据，不再通过每次请求和响应发送；
- 首部表在HTTP/2的连接存续期内始终存在，由客户端和服务器共同渐进地更新;
- 每个新的首部键－值对要么被追加到当前表的末尾，要么替换表中之前的值。

例如：下图中的两个请求， 请求一发送了所有的头部字段，第二个请求则只需要发送差异数据，这样可以减少冗余数据，降低开销。

![](/home/cyc/图片/v2-1573194744d005dd110bbeac3a9b5246_hd.jpg)



## 滑动冲突

> 一文解决Android View滑动冲突

> 作者：Flyzend

> 链接：https://www.jianshu.com/p/982a83271327

> 来源：简书

> 简书著作权归作者所有，任何形式的转载都请联系作者获得授权并注明出处。



## `Fragment` 生命周期



## # Activity 屏幕翻转时候的生命周期

onPause() 、onSaveInstanceState()、onStop()、onDestroy()、onCreate()、onStart()、onRestoreInstanceState()



## 序列化机制

#### Serializable和Parcelable

平台不同 S是Java ，P是Android的序列号接口

序列化原理不同：S将对象转化成可存储可运输的状态，P将对象分解，分解后的每一个部分都是可传递的数据类型

优缺点：S简单但效率低，开销大，序列化和反序列化都需要大量的IO操作，P高效，基于内存，但编写麻烦

使用场景：S主要用于序列化到存储设备或者通过网络设备传输，P主要用于内存的序列化



## 垃圾回收算法



四种主要的垃圾回收算法：

① 复制算法

②标记-清除算法

③标记-整理算法

④分代算法

## Bundle

Bundle主要用于传递数据；它保存的数据，是以key-value(键值对)的形式存在的。

我们经常使用Bundle在Activity之间传递数据，传递的数据可以是boolean、byte、int、long、float、double、string等基本类型或它们对应的数组，也可以是对象或对象数组。当Bundle传递的是对象或对象数组时，必须实现[Serializable 或Parcelable](http://blog.csdn.net/mer1234567/article/details/7841657)接口。





## `startService` 和 `bindService`

1. startService 生命周期是 onCreate() -> onStartCommand() -> [service is running] -> onDestroy()-> [service is shut down]
2. bindService 生命周期是 onCreate() ->onBind() -> [service is running] -> onUnbind() -> onDestroy() -> [service is shut down]

**1.  生命周期上的区别**

​    执行startService时，Service会经历onCreate->onStartCommand。当执行stopService时，直接调用onDestroy方法。调用者如果没有stopService，Service会一直在后台运行，下次调用者再起来仍然可以stopService。

​    执行bindService时，Service会经历onCreate->onBind。这个时候调用者和Service绑定在一起。调用者调用unbindService方法或者调用者Context不存在了（如Activity被finish了），Service就会调用onUnbind->onDestroy。这里所谓的绑定在一起就是说两者共存亡了。

​    多次调用startService，该Service只能被创建一次，即该Service的onCreate方法只会被调用一次。但是每次调用startService，onStartCommand方法都会被调用。Service的onStart方法在API 5时被废弃，替代它的是onStartCommand方法。

   第一次执行bindService时，onCreate和onBind方法会被调用，但是多次执行bindService时，onCreate和onBind方法并不会被多次调用，即并不会多次创建服务和绑定服务。

**既使用startService又使用bindService的情况**

如果一个Service又被启动又被绑定，则该Service会一直在后台运行。首先不管如何调用，onCreate始终只会调用一次。对应startService调用多少次，Service的onStart方法便会调用多少次。Service的终止，需要unbindService和stopService同时调用才行。不管startService与bindService的调用顺序，如果先调用unbindService，此时服务不会自动终止，再调用stopService之后，服务才会终止；如果先调用stopService，此时服务也不会终止，而再调用unbindService或者之前调用bindService的Context不存在了（如Activity被finish的时候）之后，服务才会自动停止。

​    那么，什么情况下既使用startService，又使用bindService呢？

​    如果你只是想要启动一个后台服务长期进行某项任务，那么使用startService便可以了。如果你还想要与正在运行的Service取得联系，那么有两种方法：一种是使用broadcast，另一种是使用bindService。前者的缺点是如果交流较为频繁，容易造成性能上的问题，而后者则没有这些问题。因此，这种情况就需要startService和bindService一起使用了。

​    另外，如果你的服务只是公开一个远程接口，供连接上的客户端（Android的Service是C/S架构）远程调用执行方法，这个时候你可以不让服务一开始就运行，而只是bindService，这样在第一次bindService的时候才会创建服务的实例运行它，这会节约很多系统资源，特别是如果你的服务是远程服务，那么效果会越明显（当然在Servcie创建的是偶会花去一定时间，这点需要注意）。    





## 大佬面经

#### 今日头条Android面试](https://juejin.im/entry/5b0257e76fb9a07ac0228ace)

### 第二位

#### 头条一面（38mins） 

  １.自我介绍
 ２.介绍List接口，优缺点
 ３.List只能遍历一次，删除target的方法，双链表一次的话要怎么做
 ４.Java多态，重载和重写，分别应用场景，多态的原理
 ５.事件分发机制，页面有A和B按钮，点击A的事件流程，OnTouchEvent和setOnTouchLisenner的关系
 6.Handler机制，子线程为什么不能更新UI，不同线程looper关系，介绍ThreadHandler原理和应用
 7.项目亮点
 8.写二分查找
 9.学Android，Java多久了，交流问题 



####  二面（35mins） 

  1.自我介绍
 2.热身算法两道（反转链表，情景两个线程同步实现）
 3.如何优化过度绘制
 4.自定义view机制
 5.事件分发机制
 6.交流问题
 7.不同模块引用了重复包怎么处理
 8.OKhttp和retrofit源码，使用设计模式，动态代理具体 

#### 三面（35mins） 

  1.手撕一道数组插入，扩容等算法
 2.操作系统内存管理
 3.内部类为什么可以访问外部类变量
 4.设计im服务，网络协议如何选择和设计
 5.匹配手机通讯录查找的算法该怎么设计
 6.提问问题 

### 第三位

#### 一面

- 项目介绍 

- 自定义view相关 

- 如何绘制大于父view的子view 

- 点击事件流程 

- 发送网络请求的过程 

- 网络连接失败的原因 

- http&https区别 

- https原理 

- 网络劫持的类型原理 

- java类加载过程 

- android.view由谁加载 

- **编程** 字典序第k大的数 

- MVP设计模式 

- retrofit的了解
  一小时左右 

  

#### 二面 跪○|￣|_

- 项目介绍 
- view的事件传递机制 
- activity的生命周期 
- restart方法的调用时机，按home键的调用时机，恢复的调用时机，销毁的调用时机 
- on save/restore instate的时机，谁调用，怎么调用 
- bundle的数据结构，如何存储 
- listview内点击buttom并移动的事件流完整拦截过程 
- 多点触控如何传递 
- service的意义 
- android的IPC通信方式，是否使用过 
- service如何实现跨进程通信 
- android的多线程机制 
- asynctask的原理 
- handler原理 
- app启动后是怎样运行的（我答的是mainlooper死循环） 
- 线程池的种类，有没有用过 
- http的版本有几种 
- http请求包结构，有几种ContentType 
- http返回码的分类，400和500的区别 
- **编程** 按XML格式打印view和它的子view
  一小时不到