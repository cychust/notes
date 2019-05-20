# android 窗口管制机制

> 转载 存档



在学习 WindowManager 接口的时候，了解到这个接口很重要，因为它可以直接与Window Manager（窗口管理器）进行交互，那这个 Window Manager 究竟是怎么一回事呢？

​     通过查找资料，我知道了 Window Manager 其实是一个 service（服务）。它是全局的，系统中唯一的，是独立于android应用程序的，所有android应用程序公用的 一个单独的C++服务。（这个“单独的C++服务”的说法，建议先看看：android概念空间及seviceManager 的介绍）

 

**1. 基本架构原理**

​    要了解这个服务，我们首先要对android的基本架构原理有个了解。

​    Android 是基于 C/S 模式的。在我们的根深蒂固的想法中，C/S架构就是 客户端和服务端直接通过Binder交互数据，打开Binder写入数据，通过Binder读取数据，通讯就可以完成了。如下图：

 

![img](https://pic002.cnblogs.com/images/2011/287944/2011081216574730.gif)

 

​    然而，在 Android 的概念中，Binder 是一个很低层的概念，是 Linux 内核提供的 Binder 通讯机制。上面一层根本都看不到 Binder ，而是 Activity 跟一个 Service 的对象直接通过方法调用，获取服务。

​    这个就是Android提供给我们的外特性：在Android中，要完成某个操作，所需要做的就是请求某个有能力的服务对象去完成动作，而无需知道这个通讯是怎样工作的，以及服务在哪里。所以 Andoid 的 IPC（进程间通信）在本质上属于对象请求代理架构（对象请求代理架构：CORBA ）。就是说它不仅解决了进程间的通信问题，还是一个架构，提出了一种设计理念。

 

​    既然android的IPC 的本质是CORBA，那么我们就来分析一下CORBA。

   【**CORBA**】

​    由OMG组织制订的一种标准的面向对象应用程序体系规范，或者说 CORBA体系结构是对象管理组织（OMG）为解决分布式处理环境(DCE)中，硬件和软件系统的互连而提出的一种解决方案。

​    CORBA定议了一系列API，通信协议，和 物件/服务信息模型 用于使得异质应用程序能够互相操作，这些应用程序用不同的程序语言编写，运行在不同的平台上。CORBA因此为定义明确的物件提供了平台和位置的透明性，这些物件是分布式计算平台的基础。

​    COBRA标准主要分为3个层次：对象请求代理、公共对象服务和公共设施。最底层最核心就是ORB(对象请求代理)。

​    ORB规定了分布对象的定义（接口）和语言映射，实现对象间的通讯和互操作，是分布对象系统中的"软总线"；在ORB之上定义了很多公共服务，可以提供诸如并发服务、名字服务、事务(交易)服务、安全服务等各种各样的服务；最上层的公共设施则定义了组件框架，提供可直接为业务对象使用的服务，规定业务对象有效协作所需的协定规则

​        【ORB】     

​         ORB是一个在对象间建立客户/服务器联系的中件。使用ORB，客户可以调用服务器的对象或对象中的应用，被调用的对象不要求在同一台机器上。由ORB负责进行通信，同时ORB也会在调用对象完成后返回结果。客户对象完全可以不关心服务器对象的位置，实现它所采用的具体技术和工作的硬件平台，甚至不必关心服务器对象的与服务无关的接口信息，这就大大简化了客户程序的工作。既然能够这么方便，那ORB就需要提供在不同机器间应用程序间的通信，数据转换，并提供多对象系统的无缝连接。

​    CORBA 的本质以图的形式展现就是如下：

​    

![img](https://pic002.cnblogs.com/images/2011/287944/2011081216584362.gif)

  

​    在服务端，多了个代理器：

​    

![img](https://pic002.cnblogs.com/images/2011/287944/2011081216591622.gif)

   

 

​    分析了CORBA的大体理论架构，下面我们来看一下**Android的对象代理结构**：

​    

![img](https://pic002.cnblogs.com/images/2011/287944/2011081217001580.gif)

 

在结构图中，我们可以较为清楚的把握Android的ＩＰＣ包含了如下的概念：

- 设备上下文什（ContextObject）

​        设备上下文包含关于客服端，环境或者请求中没有作为参数传递个操作的上下文信息，应用程序开发者用ContextObject接口上定义的操作来创建和操作上下文。

- Android代理：这个是指服务端在客户端的代理对象
- Binder ：Linux内核提供的Binder通讯机制（底层）
- AIDL : Android 中的远程接口

Android的外特性空间是不需要知道服务在那里，只要通过代理对象完成请求。

 

 

**2.窗口管理概述**

​    了解了Android 的基本架构原理，现在我们回到窗口管理这一块。

​    Android 的窗口管理是基于 C/S 模式的，客户端就是应用程序，服务端 就是 Window Manager服务。如下图：

 

​     

![img](https://pic002.cnblogs.com/images/2011/287944/2011081217004824.jpg)

   

​    Activity 建立一个主窗口之后，在将主窗口添加到 Window Manager 时，首先要建立 Window Manager的代理对象，并打开一个Session（session：会话；实现 IWindowSession AIDL 接口），并维持该会话（Activity 将通过该会话与 Window Manager 建立联系，这个Session 是C/S 体系的基础）。Client 通过这个Session 将 window 加入到 Window Manager 中。

​    一个完整的窗口概念包含了 View，ViewRoot，Window Manager Service，Window，Decor View，IWindow，ISession，WindowState。他们之间的关系如下：

 

​     

![img](https://pic002.cnblogs.com/images/2011/287944/2011081217011535.jpg)

   

​    Client 端的 Activity 通过 IWindowSession 会话与Window Manager Service 建立对话，而 Window Manager Service 通过 IWindow 接口访问 Client，将消息传递到Client 端，在通过消息分发渠道，将消息传递到具体的消息处理函数。（用户输入等操作最先是到 窗口管理服务，由窗口管理服务发给活动窗口，再一步步传递到焦点）。

 

 

**3. Client 端**

 

   **客户端组成：**

​       Window，View，ViewRoot，Window Manager Proxy

 

   **View：**

​       View 在 Android 中包含了 交互 和显示。

​       在Activity 在 performLaunchActivity 时，会使用Activity.attach() 建立一个 PhoneWindow 主窗口。这个主窗口的建立并不是一个重点。handleResumeActivity 真正要启动一个Activity 的时候，会将主窗口加入到 Window Manager，当然并不是主窗口本身，而是指的主窗口的 DecorView 。

​      DecorView ：实际上是一个ViewGroup ，在依存关系上来讲，对看这主窗口来讲，DecorView 是 Top-Level View 。如下图：

 

![img](https://pic002.cnblogs.com/images/2011/287944/2011081217015058.jpg)

​     View 的成员变量 mParent 用来管理 View 上级关系。而 ViewGroup 里构建了焦点管理和子 View 节点数组。这样通过 View 的mParent 和 ViewGroup的 mChildren 构建了 Android 中View直接的关系网。如下图：

 

​        

![img](https://pic002.cnblogs.com/images/2011/287944/2011081217024190.jpg)

 

 

  **Focus Path**

​     所谓的 Foucs Path 就是我们的KeyEvent 传递的路线。一般的我们的KeyEvent 在主循环中，主View 通过View的焦点记录关系传递到焦点 View。例如下图，View22是焦点，最顶层的View 通过 mFoucs 的关系链找到最后形成的路径就是 Foucs Path。如下图红线所示：

 

​       

![img](https://pic002.cnblogs.com/images/2011/287944/2011081217032710.jpg)

 

 

  **ViewRoot**

- ViewRoot 实际上是一个 Handler，ViewRoot 简历主View 与 WindowsManager 通讯的桥梁。
- ViewRoot 本质上就是一个 Handler，我们知道 Handler 的基本功能 就是处理回调，发送消息。

​     ViewRoot 通过 IWindowSession AIDL 添加窗口到 Window Manager ，而 IWindow AIDL 是Window Manager 分发消息给 Client ViewRoot 的渠道。

 

​       

![img](https://pic002.cnblogs.com/images/2011/287944/2011081217043378.jpg)

 

 

  **Window Manager Proxy**

​     Activity 在使用 getSystemService 获取 WindowManagerImpl 时，建立了一个 WindowManagerImpl 的实例，这就是 Window Manager服务 的代理。

​     wm=(WindowManagerImpl )context.getSystemService(Context.WINDOW_SERVICE);

​     并调用 wm.addview 添加窗口到 WMService 中。

​     在这个过程中客户端建立了什么样的管理框架，并如何这个会话？

​     在 Window Manager Proxy 中建立了 View，Layout，ViewRoot 散着的对应关系表。构造一个ViewRoot 就会打开一个Session，并利用 IWindowSession 简历会话上下文。如下图：

 

​      

![img](https://pic002.cnblogs.com/images/2011/287944/2011081217040480.jpg)

 

 

**4. 服务端 ---- Window Manager Service**

​    Window Manager Service 管理的窗口是应用程序的 Top-Level 窗口，这里称之为 主窗口，为什么主窗口要放到 Service 来管理呢？其实 放在一起管理是为了计算 Z-order 序列，根据应用程序的状态来显示/隐藏应用程序的窗口。

​    在 Service 维护了一个 mWindow 数组，这个 mWindow 就是 Window 的 Z-order序 数组。mWindowMap 用于记录 <Client:Binder,WindowState对象>。

​    在服务端的窗口对象叫做 WindowState 。WindowState 有一个叫做 mClient 成员变量来记录客户端 IWindow 实例，通过 IWindow 接口实例，Service可以访问客户端的信息，可以传递消息到客户端。所以说呢 IWindow 是 Service 链接 View 的桥梁。