# Day02 - 多线程

### 一、线程创建方式

线程(Thread):一个程序内部的一条执行流程

多线程:是指从软硬件上实现多条执行流程的技术

> [!CAUTION]
>
> 1.启动线程必须调用start()方法,不是调用run方法
>
> 直接调用run()方法会当成普通方法执行,此时相当于还是单线程执行,只有调用start()方法才是启动一个新的线程执行
>
> 2.不要把主线程任务放在启动子线程之前(这样才会让多线程的状态显现)



```java
//创建方式1:
class MyThread extends Thread{
    @Override
    public void run() {
       for(int i=0;i<10;i++){
           System.out.println(i+"mythread");
       }
    }
}

//创建方式2
class MyRunnable implements Runnable {
    @Override
    public void run(){
        for(int i=1;i<5;i++){
            System.out.println(i+"mythread");
        }
    }
}
//调用方式:
Runnable r = new MyRunnable();
Thread t1 = new Tread(r);
t1.start();

//匿名内部类以及Lambda表达式
new Thread(new Runnable(){
    @Override
    public void run(){
        for(int i=1;i<5;i++){
            System.out.println(i+"mythread");
        }        
    }
}).start();

new Thread(()->{
        for(int i=1;i<5;i++){
            System.out.println(i+"mythread");
        }
}).start();

//创建方式3:
//利用Callable接口,FutureTask类实现
class MyCallable implements Callable<String> {
    private int n;
    public MyCallable(int n){
        this.n=n;
    }
    @Override
    public String call() throws Exception {
        int sum=0;
        for(int i=0;i<n;i++){
            sum+=i;
        }
        return "子线程的计算结果为:"+sum;
    }
}

Callable<String> myCallable = new MyCallable(100);
//把Callable对象封装成一个真正的线程任务对象FutureTask对象
FutureTask<String> futureTask = new FutureTask<>(myCallable);
//把FutureTask对象交给线程池去执行
new Thread(futureTask).start();
try {
        //获取线程执行的结果
        String result = futureTask.get();
        System.out.println(result);
} catch (Exception e) {
        e.printStackTrace();
}
```

### 二、线程常用方法

```java
 public static void sleep(long millis)//让当前线程休眠
 //Thread.sleep(1000);
     
 public final void join()//让调用当前这个方法的线程先执行完
 //new Thread(r).join()
```

### 三、线程安全

多个线程,同时操作同一个共享资源的时候,可能会出现安全问题。

线程同步的核心思想:让多个线程先后依次访问共享资源,这样就可以避免出现线程安全问题。

线程同步的常见方案:

加锁:每次只允许一个线程加锁,加锁后才能进入访问,访问完毕后自动解锁,然后其他线程才能够继续加锁进来

1. 同步代码块:把访问贡献资源的核心代码给上锁,以此保证线程安全

```java
synchronized(同步频){
    访问共享资源的核心代码
}

//建议使用共享资源作为锁对象,对于实例方法建议使用this作为锁对象
//对于静态方法建议使用字节码(类名.class)作为锁对象
```

2. 同步方法:把访问共享资源的核心代码给上锁,以此保证线程安全

```java
修饰符 synchronized 返回值类型 方法名称(形参列表){
    操作共享资源的代码
}
//底层逻辑:如果方法是实例方法,同步方法默认this作为锁对象
//如果方法是静态方法:同步方法默认用类名.class作为对象
```

3. ```java
   class Acount{
   	private double money;
       private final Lock lk = new Reentrantlock();
       
       public void drawMoney(double money){
           String name = Thread.currentThread().getName();
           lk .lock();
           try{
               if(this.money >= money){
                   System.out.println(name + "取钱成功!");
        			this.money -= money;
                   System.out.println("剩余"+money+"元!")
               }else {
                   System.out.println(name+"取钱失败!");
               }
               
           }finally{
               lk.unlock();
           }
       }
   }
   ```

   

### 四、线程池

线程池:就是一个可以复用线程的技术

创建方法:

1.通过ThreadPoolExecutor创建线程池

```java
public ThreadPoolExecutor (
	int corePoolSize,//指定线程池的核心线程的数量
	int maximumPoolSize,//指定线程池的最大线程数量
	long keepAliceTime,//指定临时线程的存活时间
	TimeUnit unit,//指定线程池的任务队列
	BlockingQueue<Runnable> workQueue,//指定线程池的任务队列
	ThreadFactory threadFactory,//指定线程池的线程工厂
	RejectedExecutionHandler handler,//指定线程池的任务拒绝策略
)
```

```java
ExecutorService threadPoolExecutor = new ThreadPoolExecutor(
		3, 5,
        10, TimeUnit.SECONDS, new ArrayBlockingQueue<>(3),
        Executors.defaultThreadFactory(), new ThreadPoolExecutor.AbortPolicy());
```

> [!CAUTION]
>
> 临时线程什么时候创建:
>
> 核心线程都在忙,任务队列也满了(还有新任务过来),并且还可以创建临时线程,才会创建
>
> 什么时候会拒绝新任务:
>
> 核心线程和临时线程都在忙,并且任务队列已经满了,此时才会拒绝任务

2. 通过Executors创建线程池
