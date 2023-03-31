#  Android笔记

#### Glide 源码分析[Glide](https://blog.csdn.net/guolin_blog/article/details/53759439) 和相关 [分析](https://juejin.cn/post/6850037281349173261)

1. 三级缓存（网络层缓存如okhttp就不考虑了）：内存活跃资源`ActiveResources`、内存非活跃资源`LruResourceCache`、磁盘缓存`DiskLruCache`
2. [glide细支](https://juejin.cn/post/6854573209635782664#heading-1)

- [volatile关键字在Android中到底有什么用?](https://guolin.blog.csdn.net/article/details/109009649)

## 进程间通信方式

- 共享内存(ipc)
- 管道

- 消息队列
- socket

## 序列化的方式，和应用场景。

## [Android刘海屏、水滴屏全面屏适配方案](https://www.jianshu.com/p/2b8db60ba8df)

## [谈谈Error和Exception的区别](https://github.com/Moosphan/Android-Daily-Interview/issues/68)

- ```
  Error} is the superclass of all classes that represent unrecoverable
  * errors. When errors are thrown, they should not be caught by application
  ```

- ```
  {@code Exception} is the superclass of all classes that represent recoverable
  * exceptions. When exceptions are thrown, they may be caught by application
  * code.
  ```

## 什么是反射机制？反射机制的应用场景有哪些

Java 反射机制是在运行状态中，对于任意一个类，都能够知道这个类中的所有属性和方法，对于任意一个对象，都能够调用它的任意一个方法和属性；这种动态获取的信息以及动态调用对象的方法的功能称为 Java 语言的反射机制。
应用场景：

1. 逆向代码，例如反编译
2. 与注解相结合的框架，如 Retrofit
3. 单纯的反射机制应用框架，例如 EventBus（事件总线）
4. 动态生成类框架 例如Gson

## [java泛型作用](https://blog.csdn.net/jiahao1186/article/details/91411606)

## Sting为什么要设计成不可变的

1. 字符串常量池的需要

 当创建一个 String 对象时，如果此字符串已经存在于常量池中，则不会创建一个新的对象，而是引用已经存在的对象

 如果允许改变，那么将导致各种逻辑错误，比如改变一个对象将会影响另一个独立对象，严格来说，这种常量池的思想是一种优化手段

2. 允许String对象缓存 HashCode

 java 中 String 对象的哈希码会被频繁的使用，比如在 hashMap中。字符串的不变形保证了hash码的唯一性，因此可以放放心的进行缓存。这也是一种优化手段，意味着不必没说都计算新的哈希码。在 String 类中有 private int hash 来缓存hashcode

3. 安全性

 String 被许多的类来当做参数，如 网络url，文件路径path 等等，如果String 不是固定的，将会引起各种安全隐患

## [jdk1.8 hashMap源码](https://www.infoq.cn/article/DBIPV1XRorIf7SdMW0A5)

## [BlockCanary原理](http://blog.zhaiyifan.cn/2016/01/16/BlockCanaryTransparentPerformanceMonitor/)

## Java 中四种线程池的总结 

【参考】：具体的 4 种常用的线程池实现如下：（返回值都是 ExecutorService） 

+ 1、Executors.newCacheThreadPool()：可缓存线程池，先查看池中有没有以前建立的线程，

如果有，就直接使用。如果没有，就建一个新的线程加入池中，缓存型池子通常用于执行一

些生存期很短的异步型任务。

ExecutorService cachedThreadPool = Executors.newCachedThreadPool();

+ 2、Executors.newFixedThreadPool(int n)：创建一个定长线程池，以共享的无界队列方式来运

行这些线程。

ExecutorService fixedThreadPool = Executors.newFixedThreadPool(3);

+ 3、Executors.newScheduledThreadPool(int n)：创建一个定长线程池，支持定时及周期性任

务执行

ScheduledExecutorService scheduledThreadPool = Executors.newScheduledThreadPool(5);

+ 4、 Executors.newSingleThreadExecutor()：创建一个单线程化的线程池，它只会用唯一的工

作线程来执行任务，保证所有任务按照指定顺序(FIFO, LIFO, 优先级)执行。

ExecutorService singleThreadExecutor = Executors.newSingleThreadExecutor();

public ThreadPoolExecutor(int corePoolSize,

 int maximumPoolSize,

 long keepAliveTime,

 TimeUnit unit,

 BlockingQueue<Runnable> workQueue) {

 this(corePoolSize, maximumPoolSize, keepAliveTime, unit, workQueue,

 Executors.defaultThreadFactory(), defaultHandler);

 }corePoolSize:核心池大小，意思是当超过这个范围的时候，就需要将新的线程放到等待队列

中了即 workQueue；

maximumPoolSize:线程池最大线程数量，表明线程池能创建的最大线程数

keepAlivertime:当活跃线程数大于核心线程数，空闲的多余线程最大存活时间。

unit：存活时间的单位

workQueue:存放任务的队列---阻塞队列

handler:超出线程范围（maximumPoolSize）和队列容量的任务的处理程序

我们执行线程时都会调用到 ThreadPoolExecutor 的 execute()方法，现在我们来看看这个方

法的源码（就是下面这段代码了，这里面有一些注释解析），我直接来解释一下吧：在这段

代码中我们至少要看懂一个逻辑：当当前线程数小于核心池线程数时，只需要添加一个线程

并且启动它，如果线程数数目大于核心线程池数目，我们将任务放到 workQueue 中，如果

连 WorkQueue 满了，那么就要拒绝任务了

***为什么不建议使用***【强制】线程池不允许使用 Executors 去创建，而是通过 ThreadPoolExecutor 的方式，这样

的处理方式让写的同学更加明确线程池的运行规则，规避资源耗尽的风险。

说明：Executors 返回的线程池对象的弊端如下： 

1）FixedThreadPool 和 SingleThreadPool:

允许的请求队列长度为 Integer.MAX_VALUE，可能会堆积大量的请求，从而导致 OOM。 

2）CachedThreadPool 和 ScheduledThreadPool:

允许的创建线程数量为 Integer.MAX_VALUE，可能会创建大量的线程，从而导致 OOM。

## [LeakCanary内存泄漏分析](https://blog.csdn.net/dawn4get/article/details/76473524)

## hashSet

1. hashSet 内部有一个hashMap，add的时候，通过hashMap的put方法，添加的元素作为key，内部有一个static final成员变量作为value存在HashMap中，因为hashMap的键是唯一的，所以hashSet能够存不同的元素

2. 在hashSet获取元素的时候，通过hashMap对key的迭代map.keySet().iterator来获取元素

## AsyncTask 的优缺点

缺点：

1. 串行执行

2. 内存泄露

3. 结果丢失

4. 必须要执行在主线程中

## 全面屏适配

在AndroidManifest的application里面设置resizeableActivity的属性为true，并且在application节点申明最大屏幕纵横比

```
 ``<meta-data
      ``android:name=``"android.max_aspect"
      ``android:value=``"2.4"` `/>
```

## AnimatorSet动画（可以执行一些组合动画）

- play（a1）.before（a2）；是先执行a1，后执行a2，他们是有先后顺序的
- play（a1）.after（a2），是先执行a2，后执行a1

## kotlin 学习

- 定义变量**var** 可以变，定义常量**val**不可变，相当于final

- ```
  //类型后面加?表示可为空
  var age: String? = "23" 
  //抛出空指针异常
  val ages = age!!.toInt()
  //不做处理返回 null
  val ages1 = age?.toInt()
  //age为空返回-1
  val ages2 = age?.toInt() ?: -1
  ```

## 死锁的概念，其产生的四个必要条件是啥？

1. 互斥条件：线程要求对所分配的资源进行排它性控制，即在一段时间内某资源仅为一线程所占用。

2. 请求和保持条件：当线程因请求资源而阻塞时，对已获得的资源保持不放。

3. 不剥夺条件：线程已获得的资源在未使用完之前，不能剥夺，只能在使用完时由自己释放。

4. 环路等待条件：在发生死锁时，必然存在一个进程–资源的环形链。

   

# Android 实战中提高Handler发送消息的优先级

https://blog.csdn.net/hanshengjian/article/details/120398545

# 解决 Android 6.0 WebView 标题显示url的问题

```java

@Override
public void onReceivedTitle(WebView view, String title) {
    super.onReceivedTitle(view, title);
    //为解决6.0系统，这个api会调用两次，而且第一次是显示url的系统bug
    if (title != null && !view.getUrl().contains(title)) {
    //设置页面title
    }
}     

```

## Java 四种引用

【参考】

### 1.  强引用 StrongReference

如果一个对象具有强引用，那么垃圾回收器绝对不会回收它，当内存不足时宁愿抛出 OOM 

错误，使得程序异常停止。

Object object = new Object(); 即是一个强引用。

### 2. 软引用 SoftReference

如果一个对象只具有软引用，那么垃圾回收器在内存充足的时候不会回收它，而在内存不足

时会回收这些对象。软引用对象被回收后，Java 虚拟机会把这个软引用加入到与之关联的

引用队列中。

### 3. 弱引用 WeakReference

如果一个对象只具有弱引用，那么垃圾回收器在扫描到该对象时，无论内存充足与否，都会

回收该对象的内存。与软引用相同，弱引用对象被回收后，Java 虚拟机会把这个弱引用加

入到与之关联的引用队列中。

### 4. 虚引用 PhantomReference

虚引用并不决定对象生命周期，如果一个对象只具有虚引用，那么它和没有任何引用一样，

任何时候都可能被回收。虚引用主要用来跟踪对象被垃圾回收器回收的活动。与软引用和弱

引用不同的是，虚引用必须关联一个引用队列。

当垃圾回收器准备回收一个对象之前，如果发现它还具有虚引用，就会在对象回收前把这个

虚引用加入到与之关联的引用队列中。程序可以通过判断引用队列中是否加入了虚引用，来

了解被引用的对象是否将要被回收，那么就可以在其被回收之前采取必要的行动。

弱引用解决内存泄露问题

虽然 Java 有垃圾回收机制，但是只要是自己管理的内存，就应该警惕内存泄露的问题，例

如对象池、缓存中的过期对象都有可能引发内存泄露的问题。用 WeakHashMap 来作为缓

存的容器可以有效解决这一问题。WeakHashMap 和 HashMap 几乎一样，唯一的区别就是

它的键使用弱引用。当 WeakHashMap 的键标记为过期时，这个键对应的条目就会自动被

移除。这就避免了内存泄漏问题。

另外在 Android 中常常用到 Handler 消息处理机制。当使用内部类（包括匿名内部类）来

创建 Handler 时，Handler 对象会隐式的持有一个其外部类对象（通常是一个 Activity）的

引用。而 Handler 通常会开启一个耗时的工作线程（例如下载获取数据），工作线程完成任

务后，通过消息机制通知 Handler 在主线程做相应的处理。如果在工作线程执行的过程中

关闭了 Activity，Activity 应该在 GC 检查时被回收掉。但因为 Activity 被 Handler 持有引用 ， Handler 又 被 Message 持有引用， Message 被 MessageQueue 持 有 引 用 ，

MessageQueue 被 Looper 持有引用，而 Looper 是线程本地变量，线程不销毁，它就不会

被销毁。这样一个引用链，导致 Activity 无法被回收，造成内存泄漏。

1. 通过程序逻辑控制防止内存泄漏

比如在 Activity 关闭时停掉工作线程，移除消息队列中的消息对象，这样切断了 Activity 的

引用，就可以正常的被回收了。

2. 将 Handler 声明成静态内部类

静态类不持有外部类的对象，所以 Activity 可以被正常的回收。但这个时候 Handler 无法

操作 Activity 中的对象了，所以这个时候需要增加一个对 Activity 弱引用。

## [Android V1及V2签名签名区别](https://zhuanlan.zhihu.com/p/108034286)

1. MANIFEST.MF
2. CERT.SF
3. CERT.RSA

## 为什么 JDK 的动态代理只能使用接口



## ASM字节码

### WebViewSSLCheckThread(支付宝)webview证书校验 

hmsrootcas.bks

![image-20220125191350251](C:\Users\sm03\AppData\Roaming\Typora\typora-user-images\image-20220125191350251.png)

![image-20220125204507881](C:\Users\sm03\AppData\Roaming\Typora\typora-user-images\image-20220125204507881.png)

webview证书校验：

```java
public static void checkServerCertificateNew(SslErrorHandler sslErrorHandler, SslError sslError, String str, Context context, WebViewSSLCheckThread.Callback callback) {
        g.c(TAG, " error type : " + sslError.getPrimaryError());
        X509Certificate a2 = b.a(sslError.getCertificate());
        X509Certificate m = new j(context).m();
        g.b(TAG, "checkServerCertificateNew: error certificate is : " + a2);
        if (b.a(m, a2)) {
            g.c(TAG, "checkServerCertificateNew: proceed");
            if (callback != null) {
                callback.onProceed(context, str);
            } else {
                sslErrorHandler.proceed();
            }
        } else {
            g.e(TAG, "checkServerCertificateNew: cancel");
            if (callback != null) {
                callback.onCancel(context, str);
            } else {
                sslErrorHandler.cancel();
            }
        }
    }


```

a2值获取

```java
 public static X509Certificate a(SslCertificate sslCertificate) {
        byte[] byteArray = SslCertificate.saveState(sslCertificate).getByteArray("x509-certificate");
        if (byteArray != null) {
            try {
                return (X509Certificate) CertificateFactory.getInstance("X.509").generateCertificate(new 		ByteArrayInputStream(byteArray));
            } catch (CertificateException e) {
                g.a(TAG, "exception", e);
            }
        }
        return null;
    }


```

```java
public class j {
    private static final String TAG = "X509CertificateUtil";
    private static final String an = "052root";
    private static final String ao = "hmsincas.bks";
    private static final String ap = "huawei cbg application integration ca";
    private static final String b = "bks";
    private static final String k = "hmsrootcas.bks";
    private static final String l = "";
    private Context u;

    public j(Context context) {
        this.u = context;
    }

    public X509Certificate f(String str, String str2) {
        InputStream inputStream;
        try {
            KeyStore instance = KeyStore.getInstance(b);
            inputStream = this.u.getAssets().open(str);
            try {
                inputStream.reset();
                instance.load(inputStream, "".toCharArray());
                X509Certificate x509Certificate = (X509Certificate) instance.getCertificate(str2);
                f.c(inputStream);
                return x509Certificate;
            } catch (IOException | KeyStoreException | NoSuchAlgorithmException | CertificateException e) {
                e = e;
                try {
                    g.e(TAG, "loadBksCA: exception : " + e.getMessage());
                    f.c(inputStream);
                    return null;
                } catch (Throwable th) {
                    th = th;
                    f.c(inputStream);
                    throw th;
                }
            }
        } catch (KeyStoreException e2) {
            e = e2;
            inputStream = null;
        } catch (CertificateException e3) {
            e = e3;
            inputStream = null;
        } catch (IOException e4) {
            e = e4;
            inputStream = null;
        } catch (NoSuchAlgorithmException e5) {
            e = e5;
            inputStream = null;
        } catch (Throwable th2) {
            th = th2;
            inputStream = null;
            f.c(inputStream);
            throw th;
        }
    }

    public X509Certificate l() {
        return f("hmsrootcas.bks", an);
    }

    public X509Certificate m() {
        return f(ao, ap);
    }
}
```



```java
//class b
public static boolean a(X509Certificate x509Certificate, X509Certificate x509Certificate2) {
        try {
            x509Certificate2.verify(x509Certificate.getPublicKey());
            if (a(new X509Certificate[]{x509Certificate, x509Certificate2})) {
                return true;
            }
            g.e(TAG, "verify: date not right");
            return false;
        } catch (CertificateException e) {
            g.e(TAG, "verify: publickey CertificateException " + e.getMessage());
            return false;
        } catch (InvalidKeyException e2) {
            g.e(TAG, "verify: publickey InvalidKeyException " + e2.getMessage());
            return false;
        } catch (NoSuchAlgorithmException e3) {
            g.e(TAG, "verify: publickey NoSuchAlgorithmException " + e3.getMessage());
            return false;
        } catch (NoSuchProviderException e4) {
            g.e(TAG, "verify: publickey NoSuchProviderException " + e4.getMessage());
            return false;
        } catch (SignatureException e5) {
            g.e(TAG, "verify: publickey SignatureException " + e5.getMessage());
            return false;
        }
    }

```

### 注解有哪几类

## URL Base64算法

Base64编码值通过URL传输会出现问题，因为Base64编码中的“+”和“/”符号是不允许出现在URL中的。同样，符号“=”用做参数分隔符，也不允许出现在URL中，根据RFC 4648中的建议，“~”和“.”符都有可能替代“=”符号。但“~”符号与文件系统相冲突，不能使用；如果使用“.”符号，某些文件系统认为该符号连续出现两次则为错误。

# 同一对象中的两个synchronized方法，可以被同时访问吗？

不可以，因`synchronized`方法使用的锁是对象本身，一旦一个方法获得了锁，另外一个方法想要获取这个锁，就要等待锁被释放，所以**同一对象的两个`synchronized`方法不能被同时访问**

http://stackoverflow.com/questions/15438727/if-i-synchronized-two-methods-on-the-same-class-can-they-run-simultaneously

## 同一个类里面两个synchronized方法，两个线程同时访问的问题

+ 如果synchronized修饰的是静态方法，锁的是当前类的class对象，进入同步代码前要获得当前类对象的锁；

+ 普通方法，锁的是当前实例对象，进入同步代码前要获得的是当前实例的锁；

+ 同步代码块，锁的是括号里面的对象，对给定的对象加锁，进入同步代码块库前要获得给定对象锁；

+ 如果两个线程访问同一个对象的synchronized方法，会出现竞争，如果是不同对象，则不会相互影响

# java-同时最多只允许5个线程执行某个方法（Semaphore、线程池）

```java
public class SemaphoreDemo {

    static class TaskThread extends Thread {

        Semaphore semaphore;

        public TaskThread(Semaphore semaphore) {
            this.semaphore = semaphore;
        }

        @Override
        public void run() {
            try {
                semaphore.acquire();
                System.out.println(getName() + " acquire");
                Thread.sleep(1000);
                semaphore.release();
                System.out.println(getName() + " release ");
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }

    public static void main(String[] args) {
        int threadNum = 5;
        Semaphore semaphore = new Semaphore(2);//最多允许2个线程同时访问该方法
        for (int i = 0; i < threadNum; i++) {
            new TaskThread(semaphore).start();
        }
    }

}
```

## 策略设计模式

```java
public class StrategyModel {

    CalculateStrategy calculateStrategy;

    public void main(String[] args) {
        StrategyModel strategyModel = new StrategyModel();
        strategyModel.setStrategy(new BusStrategy());
        System.out.println("花费 " + strategyModel.calculatePrice(10) + " 元");


    }

    public void setStrategy(CalculateStrategy calculateStrategy) {
        this.calculateStrategy = calculateStrategy;
    }

    public int calculatePrice(int km) {
        return this.calculateStrategy.calculatePrice(km);
    }

    public interface CalculateStrategy {
        /**
         * 按距离计算价格
         *
         * @param km
         * @return
         */
        int calculatePrice(int km);
    }

    public class BusStrategy implements CalculateStrategy {

        @Override
        public int calculatePrice(int km) {
            return km * 5;
        }
    }


    class SubwayStrategy implements CalculateStrategy {

        @Override
        public int calculatePrice(int km) {
            return km * 10;
        }
    }

}
```

### View的事件分发

+ 事件优先级：onTouchListener >>> onTouchEvent >>> setOnLongClickListener >>> OnClickListerner

+ ```
  dispatchTouchEvent>>> onInterceptTouchEvent>>>onTouchEvent
  ```

```undefined
1、DexClassLoader可以加载jar/apk/dex，可以从SD卡中加载未安装的apk 
2、PathClassLoader只能加载系统中已经安装过的apk
```

# Android - 自动化埋点

https://blog.csdn.net/gykimo/article/details/24772853

### 开启线程的三种方式

+ 继承Thread类，重写run()方法，在run()方法体中编写要完成的任务 new Thread().start();
+ 实现Runnable接口，实现run()方法 new Thread(new MyRunnable()).start();
+ 实现Callable接口MyCallable类，实现call()方法，使用FutureTask类来包装Callable对象，使用FutureTask对象作为Thread对象的target创建并启动线程；调用FutureTask对象的get()方法来获得子线程执行结束后的返回值。

### 屏幕适配

+ dp适配
+ 布局适配
+ 图片适配：点9图、自定义View或者shape
+ imageVIew设置scaleType
+ 百分比适配

### 加载超大图片

+ BitmapRegionDecoder(Bitmap局部解码)
+ 使用inSampleSize进行压缩
+ ARGB-->RGB

待学习：BlockingQueue/CopyOnWriteList/ConcurrentHashMap、CountDownLatch/CyclicBarrier/Semaphore等

**CopyOnWriteArrayList**

支持高效率并发且是线程安全的,读操作无锁的ArrayList，**合适读多写少的场景**

### 对于Android 的安全问题，你知道多少

①错误导出组件

② 参数校验不严

③WebView引入各种安全问题,webview中的js注入

④不混淆、不防二次打包

⑤明文存储关键信息

⑦ 错误使用HTTPS

⑧山寨加密方法

⑨滥用权限、内存泄露、使用debug签名

# [Android 如何检查apk是否已经签名，V1、V2、V3和V4签名？](https://www.cnblogs.com/onelikeone/p/15010397.html)

命令：apksigner verify -verbose -print-certs *.apk

### ThreadLocal

每个线程会有一个ThreadLocalMap，它的key是ThreadLocal对象，值为传入的数据

### adb获取包名以及当前运行的activity
+ adb shell dumpsys window | findstr mCurrentFocus

+ adb shell dumpsys activity top

  

### 启动mysql:以管理员身份运行：**net start mysql**

# IDEA调试小技巧 —— Evaluate调试工具

Evaluate Expression（快捷键alt+F8）并运行指定代码



## 特殊时期，界面一键置灰

~~~java
public class SaturationView {
    private final Paint paint = new Paint();
    private final ColorMatrix cm = new ColorMatrix();
    private SaturationView(){
    }
    private static SaturationView instance;
    public static SaturationView getInstance(){
        synchronized (SaturationView.class) {
            if (instance == null) {
                instance = new SaturationView();
            }
        }
        return instance;
    }
    // view 需要置灰的view，saturation=0为置灰回
    public void saturationView(View view, float saturation){
        cm.setSaturation(saturation);
        paint.setColorFilter(new ColorMatrixColorFilter(cm));
        view.setLayerType(View.LAYER_TYPE_HARDWARE, paint);
    }
}
~~~



## 手动签名命令

java -jar apksigner.jar sign --ks .\testHz.jks .\xxbank.apk



# Android的Shortcut功能

# [Inner join vs Where](https://stackoverflow.com/questions/121631/inner-join-vs-where)

inner join 和where查询的区别

# Inner join ,left join

内联结是两张表中都存在才能关联出来。而左联结的意思就是我们的主表中的所有行都会展示出来







