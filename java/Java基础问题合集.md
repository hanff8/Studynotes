## 1. **String ...** 与 **String[]** 的区别
String ... 是Java5 开始对方法参数的一种新写法，叫可变长度参数。
假设现在有两个方法分别是
	test()与test(String...)
```java
public class Test003 {  

    private Test003(){  
        test();  
        test("a","b");
        test(new String[]{"aaa","bbb"});
        test("ccc");  
    }  

    private void test(){  
        System.out.println("test");   
    }  

    private void test(String...strings){  
        for(String str:strings){  
            System.out.print(str + ", ");  
        }  
        System.out.println();  
    }  
    public static void main(String[] args) {  
        new Test003();  
    }  

}  
```
假设我们没有参数去调用test(String...)，即test()，
我们在调用test()的时候会优先调用test()方法。只有当没有test()函数的时候，我们调，程序才会走test(String ...)

## 2. 什么是fail-fast
[什么是fail-fast - 程序员自由之路 - 博客园 (cnblogs.com)](https://www.cnblogs.com/54chensongxia/p/12470446.html)

### 2.1. 如何禁止指令重排序？
- volatile关键字修饰
- 实现Unsafe类中的方法
```java
public native void loadFence();
public native void storeFence();
public native void fullFence();

```
### 2.2. volatile关键字
保证可见性但是不保证原子性

## 3. 经常见到的上下文切换是什么
[深入理解Linux的CPU上下文切换-腾讯云开发者社区-腾讯云 (tencent.com)](https://cloud.tencent.com/developer/article/1897179)
简单来说就是：
	`上下文切换`，就是先把前一个任务的 `CPU` 上下文保存起来，然后加载新任务的上下文到这些寄存器和程序计数器，最后再跳到程序计数器所指的新位置，运行新任务。而这些保存下来的上下文，会存储在系统内核中，并在任务重新调度执行时再次加载进来。这样就能保证任务原来的状态不受影响，让任务看起来还是连续运行

## 4. 乐观锁与悲观锁

### 4.1. 乐观锁是什么
	乐观锁总是假设最好的情况，认为共享资源每次被访问的时候不会出现问题，线程可以不停地执行，无需加锁也无需等待（太乐观了），只是在提交修改的时候去验证对应的资源（也就是数据）是否被其它线程修改了（具体方法可以使用版本号机制或 CAS 算法）。

---

著作权归JavaGuide(javaguide.cn)所有 基于MIT协议 原文链接：https://javaguide.cn/java/concurrent/java-concurrent-questions-02.html
#### 4.1.1. 如何实现乐观锁
- 版本号机制
	人话说就是：每个线程在修改数据之后都会对版本号加一或者修改，只有当前线程的修改之后的版本号与当前数据的版本号相同的时候，改数据的操作才能被写入
- CAS算法
	Compare And Swap (比较与交换)，用于实现乐观锁，被应用于各种框架中
	CAS的思想很简单，就是用一个预期值与要更新的变量值进行比较，两值相同才会更新
##### 4.1.1.1. CAS
CAS 涉及到三个操作数：
- **V**：要更新的变量值(Var)
- **E**：预期值(Expected)
- **N**：拟写入的新值(New)

**存在的问题**
- ABA问题：
	简单来说就是当准备赋值的时候发现将要更新的值 E=V ，这时候无法确定是否被修改过
	可以通过**版本号**或者**时间戳**来解决
	JDK1.5 之后的 **AtomicStampedReference** 中的 **compareAndSet** 就通过时间戳来解决ABA问题

-  循环时间长开销大
CAS 经常会用到**自旋操作**来进行重试，也就是不成功就一直循环执行直到成功。如果长时间不成功，会给 CPU 带来非常大的执行开销。

- 只能保证一个共享变量的原子操作
CAS 只对单个共享变量有效，当操作涉及跨多个共享变量时 CAS 无效。但是从 JDK 1.5 开始，提供了`AtomicReference`类来保证引用对象之间的原子性，你可以把多个变量放在一个对象里来进行 CAS 操作.所以我们可以使用锁或者利用`AtomicReference`类把多个共享变量合并成一个共享变量来操作

## 5. synchronized

可修饰：
- 实例方法
	给当前对象实例加锁，进入同步代码前要获得 **当前对象实例的锁** 。
- 静态方法
	给当前类加锁，会作用于类的所有对象实例 ，进入同步代码前要获得 **当前 class 的锁**。
	这是因为静态成员不属于任何一个实例对象，归整个类所有，不依赖于类的特定实例，被类的所有实例共享。
- 代码块
	对括号里指定的对象/类加锁：
	
	- `synchronized(object)` 表示进入同步代码库前要获得 **给定对象的锁**。
	- `synchronized(类.class)` 表示进入同步代码前要获得 **给定 Class 的锁**
	
	```java
	synchronized(this) {
	    //业务代码
	}
	
```

**构造方法不能使用 synchronized 关键字修饰。**


## 线程池

**使用线程池的好处**：

- **降低资源消耗**。通过重复利用已创建的线程降低线程创建和销毁造成的消耗。
- **提高响应速度**。当任务到达时，任务可以不需要等到线程创建就能立即执行。
- **提高线程的可管理性**。线程是稀缺资源，如果无限制的创建，不仅会消耗系统资源，还会降低系统的稳定性，使用线程池可以进行统一的分配，调优和监控。



