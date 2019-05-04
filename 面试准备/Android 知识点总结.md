# Android 知识点总结







## 大佬博客

[Gityuan](<http://gityuan.com/>)





## 优秀文章



[Android大厂面试题锦集(BAT TMD JD 小米)](<https://www.jianshu.com/p/cf5092fa2694>)





## 内存泄露

#### [Android 内存泄露查找和解决](<https://juejin.im/entry/589542ed2f301e0069054007>)

#### [Android性能优化：手把手带你全面实现内存优化](<https://www.jianshu.com/p/9745a9375191>)



/****/

非常好的文章

#### [Android 内存泄漏总结](<https://yq.aliyun.com/articles/3009?spm=5176.100239.yqblog1.32.fgxOiC>)

/****/



##### 工具 

- `LeakCanary`
- `MAT(Memory Analysis Tools)`
- `Heap Viewer`
- `Allocation Tracker`
- `Android Studio 的 Memory Monitor`

##### 内存抖动



## ANR (应用无响应)



#### [彻底理解安卓应用无响应机制](<http://gityuan.com/2019/04/06/android-anr/>)





## Activity 

#### Activity 1 开启Activity 2再回退Activity1 的流程



onCreate()[$1] -> onStart()[$1] -> onResume()[$1] ----------开启Activity2-------> onPause()[$1] -> onCreate()[$2] -> onStart()[$2] -> onResume()[$2] -> onStop()[$1] ----->回退---> onPause()[$2] ->  onRestart()[$1] -> onStart()[$1] ->onResume()[$1] ->  onStop()[$2] ->onDestroy() [$2] 





## `App`启动流程

[Android Application启动流程分析](<https://www.jianshu.com/p/a5532ecc8377>)





## ListView 和 RecyclerView

[RecyclerView缓存原理，有图有真相](<https://juejin.im/post/5b79a0b851882542b13d204b>)

[手摸手第二弹，可视化 RecyclerView 缓存机制](<https://juejin.im/post/5a5d3d9b518825734216e1e8>)



[Android ListView 与 RecyclerView 对比浅析--缓存机制](<https://mp.weixin.qq.com/s/-CzDkEur-iIX0lPMsIS0aA>)



## AMS和WMS



**Activity与WIndow：**

- Activity只负责生命周期和事件处理
- Window只控制视图
- 一个Activity包含一个Window，如果Activity没有Window，那就相当于Service

**AMS与WMS：**

- AMS统一调度所有应用程序的Activity
- WMS控制所有Window的显示与隐藏以及要显示的位置









## 框架



### Rxjava

[详解 RxJava 的消息订阅和线程切换原理](<https://juejin.im/post/5b1fbd796fb9a01e8c5fd847#heading-29>)





## 设计模式



#### [责任链模式](https://github.com/Blankj/AndroidOfferKiller/blob/master/design-patterns/责任链模式.md)

