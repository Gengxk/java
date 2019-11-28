# 锁

### 锁
>  线程之间对锁是存在竞争关系的，如果都交给操作系统，并发性能时非常低下的，所以虚拟机会在操作系统挂起线程之前，先内部解决竞争关系，在竞争不激烈的时候避免竞争。

   - 偏向锁
>   如果程序没有竞争，就取消之前已经取得锁的线程同步操作。也就是说，如果一个线程已经获得了锁，就进入偏向锁模式，如果再要使用锁就不需要在请求，之间有别的线程要请求锁，则锁会推出偏向模式。   
++可使用参数：-XX:+UseBiasedLocking++   
++可禁止偏向：-XX:-UseBiasedLocking++

```
/***
-server -XX:+UseBiasedLocking 
-XX:BiasedLockingStartupDelay=0（在虚拟机启动后立即启动） 
-Xms512m -Xmx512m
***/

//如果在线程竞争非常激烈的情况下不要使用偏向模式
//未加参数1775ms，加参数后203ms
public class Biased{

    public static List<Integer> numbers = new Vector(); 
    
    public static void main(String[] args){
        long startTime = System.currentTimeMillis();
        int startCount = 0;
        int startNumber = 0;
        while (startCount<10000000){
            numbers.add(startNumber);
            startNumber+=2;
            startCount++;
        }
        long endTime = System.currentTimeMillis();
        System.out.println(endTime-startTime)
    }
    
}
```

   - 轻量锁
> 如果偏向锁失败，虚拟机会让线程请求轻量锁，由虚拟机内部的一个BasicObjectLock的对象实现，通过cas加锁，如果失败会膨胀为(锁膨胀)重量锁。
   
   - 自旋锁
>  锁膨胀后，线程在操作系统层面可能会被挂起，这样线程上下文切换性能损失很大。因此在膨胀后，应该让线程进入临界区，避免被操作系统挂起，自旋锁是一种有效的手段。使用自旋锁的线程可以在没有取得所得时候执行若干个空循环，避免被操作系统挂起，如果循环执行完后还是没有获得锁，就会挂起。 **自旋锁适合在锁竞争不是很激烈和单线程执行时间很短的时候使用**。
 
   
