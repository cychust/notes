# Android 面试准备笔记



## Handler 系列



#### 在子线程里直接使用Handler，会发生什么？

子线程默认不会提供一个Looper ，需要调用Looper.prepare(); 之后Looper.loop();

事例代码：

```java
class LooperThread extends Thread {
   public Handler mHandler;

   public void run() {        
   		Looper.prepare();
        mHandler = new Handler() {
            public void handleMessage(Message msg) {                // process incoming messages here
            }
        };          
        Looper.loop();
    }
}
```



#### Looper.loop() 无限循环，为什么不会导致应用卡死？

这里涉及 **Linux pipe/epoll 机制** 简单说就是在主线程的MessageQueue没有消息时，便阻塞在loop的queue.next()中的nativePollOnce()方法里，此时主线程会释放CPU资源进入休眠状态，直到下个消息到达或者有事务发生，通过往pipe管道写端写入数据来唤醒主线程工作。这里采用的epoll机制，是一种IO多路复用机制，可以同时监控多个描述符，当某个描述符就绪(读或写就绪)，则立刻通知相应程序进行读或写操作，本质同步I/O，即读写是阻塞的。 **所以说，主线程大多数时候都是处于休眠状态，并不会消耗大量CPU资源。**

[Android中为什么主线程不会因为Looper.loop()里的死循环卡死？](https://www.zhihu.com/question/34652589)



#### 如果多次start这个IntentService，它又如何判断什么时候结束呢

当onHandleIntent方法执行结束之后，IntentService会尝试通过stopSelf(int startId)来尝试停止服务。之所以不用stopSelf()来停止服务，是因为stopSelf()会立刻停止服务，而stopSelf(int startId)则会等待所有的消息都处理完毕才回终止服务。一般来说，stopSelf(int startId)在尝试停止服务之前会判断最近启动服务的次数是否和startId相等，如果相等则立刻停止服务。



#### Android 为什么在` onCreate() `和 ` onResume()` 中获取不到控件的宽高?

因为Activity在执行完oncreate，onResume之后才创建ViewRootImpl,ViewRootImpl进行View的绘制工作, 可在AppThread类中的handleResumeActivity()里了解在onResume()调用之后进行绘制







## Java基础系列

