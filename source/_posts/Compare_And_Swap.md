title: 我们常说的 CAS 自旋锁是什么
date: 2019-03-23 11:10:00
tags:
- Java
- Golang
categories:
- 并发编程

---
~~太长不看系列~~

CAS（Compare and swap），即比较并交换，也是实现我们平时所说的自旋锁或乐观锁的核心操作。

它的实现很简单，就是用一个预期的值和内存值进行比较，如果两个值相等，就用预期的值替换内存值，并返回 true。否则，返回 false。

<!-- more -->

**转载声明: 本篇内容为转载，甚至是转载的转载，仅用于学习交流或者阅读整理备忘** *[原博主文章](https://www.cnblogs.com/fengzheng/p/9018152.html)*


具体步骤如下:

1. 需要读写的内存值V
2. 进行比较的值A
3. 拟写入的新值B

> 当且仅当 V 的值等于 A时，CAS通过原子方式用新值B来更新V的值，否则不会执行任何操作（比较和替换是一个原子操作）。一般情况下是一个自旋操作，即不断的重试。

保证原子操作
任何技术的出现都是为了解决某些特定的问题， CAS 要解决的问题就是保证原子操作。原子操作是什么，原子就是最小不可拆分的，原子操作就是最小不可拆分的操作，也就是说操作一旦开始，就不能被打断，知道操作完成。在多线程环境下，原子操作是保证线程安全的重要手段。举个例子来说，假设有两个线程在工作，都想对某个值做修改，就拿自增操作来说吧，要对一个整数 i 进行自增操作，需要基本的三个步骤：

1、读取 i 的当前值；

2、对 i 值进行加 1 操作；

3、将 i 值写回内存；

假设两个进程都读取了 i 的当前值，假设是 0，这时候 A 线程对 i 加 1 了，B 线程也 加 1，最后 i 的是 1 ，而不是 2。这就是因为自增操作不是原子操作，分成的这三个步骤可以被干扰。如下面这个例子，10个线程，每个线程都执行 10000 次 i++ 操作，我们期望的值是 100,000，但是很遗憾，结果总是小于 100,000 的。

```Java   
    static int i = 0;
    
    public static void add(){
        i++;
    }
    
    private static class Plus implements Runnable{

        @Override
        public void run(){
            for(int k = 0;k<10000;k++){
                add();
            }
        }
    }
    
    public static void main(String[] args) throws InterruptedException{
        Thread[] threads = new Thread[10];
        for(int i = 0;i<10;i++){
            threads[i] = new Thread(new Plus());
            threads[i].start();
        }

        for(int i = 0;i<10;i++){
            threads[i].join();
        }
        System.out.println(i);
    }
```
既然这样，那怎么办。没错，也许你已经想到了，可以加锁或者利用 synchronized 实现，例如，将 add() 方法修改为如下这样：
```Java
public synchronized static void add(){
        i++;
    }
```
或者，加锁操作，例如下面使用 ReentrantLock （可重入锁）实现。
```Java
private static Lock lock = new ReentrantLock();
    public static void add(){
        lock.lock();
        i++;
        lock.unlock();
    }
```
CAS 实现自旋锁
既然用锁或 synchronized 关键字可以实现原子操作，那么为什么还要用 CAS 呢，因为加锁或使用 synchronized 关键字带来的性能损耗较大，而用 CAS 可以实现乐观锁，它实际上是直接利用了 CPU 层面的指令，所以性能很高。

上面也说了，CAS 是实现自旋锁的基础，CAS 利用 CPU 指令保证了操作的原子性，以达到锁的效果，至于自旋呢，看字面意思也很明白，自己旋转，翻译成人话就是循环，一般是用一个无限循环实现。这样一来，一个无限循环中，执行一个 CAS 操作，当操作成功，返回 true 时，循环结束；当返回 false 时，接着执行循环，继续尝试 CAS 操作，直到返回 true。

其实 JDK 中有好多地方用到了 CAS ,尤其是java.util.concurrent包下，比如 CountDownLatch、Semaphore、ReentrantLock 中，再比如 java.util.concurrent.atomic 包下，相信大家都用到过 Atomic* ，比如 AtomicBoolean、AtomicInteger 等。

这里拿 AtomicBoolean 来举个例子，因为它足够简单。
```Java
public class AtomicBoolean implements java.io.Serializable {
    private static final long serialVersionUID = 4654671469794556979L;
    // setup to use Unsafe.compareAndSwapInt for updates
    private static final Unsafe unsafe = Unsafe.getUnsafe();
    private static final long valueOffset;

    static {
        try {
            valueOffset = unsafe.objectFieldOffset
                (AtomicBoolean.class.getDeclaredField("value"));
        } catch (Exception ex) { throw new Error(ex); }
    }

    private volatile int value;
    
    public final boolean get() {
        return value != 0;
    }

    public final boolean compareAndSet(boolean expect, boolean update) {
        int e = expect ? 1 : 0;
        int u = update ? 1 : 0;
        return unsafe.compareAndSwapInt(this, valueOffset, e, u);
    }
   
  }
```
这是 AtomicBoolean 的部分代码，我们看到这里面又几个关键方法和属性。

1、使用了 sun.misc.Unsafe 对象，这个类提供了一系列直接操作内存对象的方法，只是在 jdk 内部使用，不建议开发者使用；

2、value 表示实际值，可以看到 get 方法实际是根据 value 是否等于0来判断布尔值的，这里的 value 定义为 volatile，因为 volatile 可以保证内存可见性，也就是 value 值只要发生变化，其他线程是马上可以看到变化后的值的；下一篇会讲一下 volatile 可见性问题，欢迎关注

3、valueOffset 是 value 值的内存偏移量，用 unsafe.objectFieldOffset 方法获得，用作后面的 compareAndSet 方法；

4、compareAndSet 方法，这就是实现 CAS 的核心方法了，在使用 AtomicBoolean 的这个方法时，只需要传递期望值和待更新的值即可，而它里面调用了 unsafe.compareAndSwapInt(this, valueOffset, e, u) 方法，它是个 native 方法，用 c++ 实现，具体的代码就不贴了，总之是利用了 CPU 的 cmpxchg 指令完成比较并替换，当然根据具体的系统版本不同，实现起来也有所区别，感兴趣的可以自行搜一下相关文章。

使用场景
CAS 适合简单对象的操作，比如布尔值、整型值等；
CAS 适合冲突较少的情况，如果太多线程在同时自旋，那么长时间循环会导致 CPU 开销很大；
比如 AtomicBoolean 可以用在这样一个场景下，系统需要根据一个布尔变量的状态属性来判断是否需要执行一些初始化操作，如果是多线程的环境下，避免多次重复执行，可以使用 AtomicBoolean 来实现，伪代码如下：
```Java
private final static AtomicBoolean flag = new AtomicBoolean();
    if(flag.compareAndSet(false,true)){
        init();
    }
```
比如 AtomicInteger 可以用在计数器中，多线程环境中，保证计数准确。

ABA问题
CAS 存在一个问题，就是一个值从 A 变为 B ，又从 B 变回了 A，这种情况下，CAS 会认为值没有发生过变化，但实际上是有变化的。对此，并发包下倒是有 AtomicStampedReference 提供了根据版本号判断的实现，可以解决一部分问题。

**实现延伸**
[原链接](https://www.jianshu.com/p/f3a6b514ce74)

Golang实现(可重入)自旋锁
```Golang
type spinLock struct{
    owner int
    count int
}

func (sl *spinLock) Lock() {
    me := GetGoroutineId()
    if sl.owner == me {// 如果当前线程已经获取到了锁，线程数加一，然后返回
        sl.count++
        return  
    } 
    // 如果没有获取到锁，则通过CAS自旋
    for !atomic.CompareAndSwapUint32((*uint32)(sl), 0, 1){
        runtime.Gosched()
    }
}

funt (sl *spinLock) Unlock() {
    if sl.owner != GetGoroutineId() {
        painc("illegalMonitorStateError")
    }
    if sl.count > 0 {//如果count大于0，则表明线程已经多次获取到锁，则通过count减1，模拟释放锁行为
        sl.count--
    }else {//如果count=0, 可以将锁释放，这样就能保证获取锁和释放锁到行为一致
        atomic.StoreUint32((*uint32)(sl), 0)

    }

}

func GetGoroutineId() int {
    defer func() {
        if err := recover; err != nil {
            fmt.Println("painc recover:panic info:%v", err)
        } 
    }()

    var buf [64]byte
    n := runtime.Stack(buf[:], false)
    idField := strings.Fields(strings.TrimPrefix(buf[:]), "goroutine")
    id, err != strconv.Atoi(idField)
    if err != nil {
        painc(fmt.Println("cannot get goroutine, %v", err))
    }
    return id
}

func NewSpinLock() sync.Locker {
    var lock spinLock
    return &lock
}
```
----- 
阅读笔记: 
    通过代码实现调用CPU底层的指令是最高效的，但是也要注意有可能引发的副作用。
-----
阅读拓展:
    [浅谈偏向锁,轻量级锁,重量级锁](https://monkeysayhi.github.io/2018/01/02/%E6%B5%85%E8%B0%88%E5%81%8F%E5%90%91%E9%94%81%E3%80%81%E8%BD%BB%E9%87%8F%E7%BA%A7%E9%94%81%E3%80%81%E9%87%8D%E9%87%8F%E7%BA%A7%E9%94%81/)
