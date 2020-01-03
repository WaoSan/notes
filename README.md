# Android-
学习笔记
+ 定时任务使用ScheduledThreadPoolExecutor而不能使用timer具体原因参考[https://blog.csdn.net/diaorenxiang/article/details/38827409]
+ 线程池创建
```
//创建一个定长线程池，可控制线程最大并发数，超出的线程会在队列中等待
Executors.newFixedThreadPool
//创建一个单线程化的线程池，它只会用唯一的工作线程来执行任务，保证所有任务按照指定顺序(FIFO, LIFO, 优先级)执行
Executors.newSingleThreadExecutor
//创建一个可缓存线程池，如果线程池长度超过处理需要，可灵活回收空闲线程，若无可回收，则新建线程
Executors.newCachedThreadPool
//创建一个定长线程池，支持定时及周期性任务执行
Executors.newScheduledThreadPool
//创建一个单线程化的线程池，支持定时及周期性任务执行
Executors.newSingleThreadScheduledExecutor
```
+ 电报NotificationCenter对监听的设计,使用不同的监听集合,
```
    private SparseArray<ArrayList<Object>> observers = new SparseArray<>();
    private SparseArray<ArrayList<Object>> removeAfterBroadcast = new SparseArray<>();
    private SparseArray<ArrayList<Object>> addAfterBroadcast = new SparseArray<>();
```
当正在执行监听时,增加新的监听则加到addAfterBroadcast集合里,待执行完监听事件把addAfterBroadcast加到observers集合里,这样有效避免了一个集合同时进行添加和移除操作
+ 换肤(Android-skin-support)
+ 设计模式
    + [状态模式](https://juejin.im/post/5de1db1c51882560a2323966#heading-9)
    + [策略模式](https://juejin.im/post/5def654f51882512302daeef)
    + [责任链模式](https://www.jianshu.com/p/7fa31c57cbb5)
    + [代理模式]()
