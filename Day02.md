# Day02 - 多线程

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
class MyThread extends Thread{
    @Override
    public void run() {
       for(int i=0;i<10;i++){
           System.out.println(i+"mythread");
       }
    }
}
```
