# 基本上只講重點

> 不錯的網站
> 
> [Java 多執行序 - HackMD](https://hackmd.io/@AlienHackMd/ryrjSW1z8) 
> 
> [JUC原子类: CAS, Unsafe和原子类详解 | Java 全栈知识体系 (pdai.tech)](https://pdai.tech/md/java/thread/java-thread-x-juc-AtomicInteger.html) 感覺很深入@@" < 終究應該會用到 >

> 按照thread CH11開始往下寫的

## 觀念:

- main區的變數可以thread裡面呼叫 但要遵守匿名函數原則
  
  只能夠使用 final 陣列或List這種 外部不改變 內部改變的物件!

## Thread

- 想創建thread 需要使用`var a=new thread( O )`

- `O` 的地方應該要放入實作 `Runnable` 的類別

- 然後 a.start ( ) 就會開新`thread`執行run ( ) 定義的動作 !

## Semaphore

- `p1102_Semaphore.java`有講哦

- 這邊簡單總結
  
  基本上就是 
  
  `semaphore.acquire()` 
  
  可以阻塞 run( ) 執行 然後 ，如果哪天
  
  `獲得許可`  才能繼續執行! 

- `許可發放` : 另外一個thread 中使用
  
  `semaphore.release()`

- <font style="color:lightgreen">幾個注意事項</font>. 
  
  1. 該寫法保證烏龜等到兔子兩千才會開始跑。
  
  2. 但CPU不一定優先處理烏龜所以未必2000後下一位是烏龜跑步。

## Daemon /ˈdiː.mən/

- `x.setDaemon(true);`  一定先set 後 start
  
  `x.start();` 
  
  執行之後 
  
  - 被打斷
  
  - 主程序結束    
  
  就會消失

## Interrupted

- `p1110.java`

- 中斷操作
  
  ```java
  @Override
  public void run() {
      for(int i = 0; i < 100; i++) {
          System.out.println("i: " + i);
  
          if(this.isInterrupted()) {
              System.out.println("isInterrupted Get signal");
              // 取得中斷訊號，但不清除
          } else {
              System.out.println("Working");
          }
  
      }
  }
  ```
  
  這邊中斷由於沒有break， 所以 i+1繼續執行，而且之後
  
  - <font style="color:lightgreen">還是保留被中斷狀態</font>因為我使用 `if(Thread.isinterrupted())`  除非
  
  - 如果使用` if ( Thread.interrupted() )` 那i+1就清除狀態
  
  - try/catch 捕捉 `InterruptedException` 異常也會清除狀態！

- `x.interrupted()` 這個可以中斷但是有條件

- run 裡面有檢查 Thread.isInterrupted() 

- 有中斷操作 NIO 操作或者 
  
  - sleep()
  
  - wait()
  
  - join() 

- <font style="color:lightgreen">需要自己判斷是否被中斷 或者自己處理 例外</font>.

- 如果只想中斷其他線程 並讓它知道，還有其他方式
  
  - 共享變量或者對象 檢查它的狀態
  
  - 同步工具 (告知別人可以終止某行為之類 )
    
    - `ReentrantLock`
    
    - `Semaphore`
    
    - `CountDownLatch` 

## Join

- `p1111.java`

- Main Thread 已經call 很多個thread 

- `b.join ( )` 則是block 呼叫者直到 b 結束run()

- `b.join(1)`  
  
  讓 b 先執行 1毫秒  
  
  如果`提早執行完畢` 也會繼續執行其他工作
  
  <font style="color:lightgreen">呼叫的是哪個thread 那個thread就會讓出時間 </font>.
  
  例如我是Main_Thread 所以 main會暫停並讓出執行時間

- <font style="color:lightgreen">參考 - > </font>. `joinInOtherThread()`

## ThreadGroup

- `p1115` 

- `var tg1=new ThreadGroup("TG1");`

- `var t1=new Thread(tg1,()->{ run的匿名 } ;`

- 新建的thread可依此分群!
  
  ```java
  var threads=new Thread[tg1.activeCount()]; 
  var ts=tg1.enumerate(threads);
  ```

- `activeCount` 用來debug的 計算活的thread數量

- `enumerate` tg1活著的thread複製進去threads 
  
  使用activeCount會因為sleep所以會有+0 不用在意

- <font style="color:lightgreen">預設例外 - > </font>.  `uncaughtException` `p1116` 

## UncaughtExceptionHandler

- `p1116_b.java` 有使用

- `thread1.setUncaughtExceptionHandler.....`

- 類似之前群組使用@override 指定預設捕捉給個人
  
  ```java
  var group=new ThreadGroup("宅宅群組")
      {
          @Override
          public void uncaughtException(Thread t, Throwable e) {
          /*super.uncaughtException(t, e);*/
  
          System.out.printf("【Bug for %s 】: %s%n",t.getName(),e.getMessage());
          }
      };
  ```

## Synchronized 關鍵字

- `p1119.java` 

- 如果一個類別的方法有使用 `synchronized` 其方法 一次只會被一個thread 呼叫，另一個人必須等待。

- 如果有兩個執行序對同一個物件的不同方法呼叫，假設兩個方法都是`synchronized`  則只有一人可以得到權限，課本上有寫說，除非synchronized( ) 使用其他物件做為鎖，否則預設方法層級使用的鎖 = this。  會導致別的同步方法無法被呼叫 !

試著使用下面的方式 則可以讓synchronized 不要 鎖死全部方法

 <font style="color:lightgreen">( 一次只能一個同步方法被執行 ， 太嚴格 ! ) </font>

```java
public class Material {
    private int data1 = 0;
    private int data2 = 0;
    private Object lock1 = new Object();
    private Object lock2 = new Object();

    public void doSome() {
        ...
        synchronized(lock1) {
            ...
            data1++;
            ...
        }
        ...
    }

    public void doOther() {
        ...
        synchronized(lock2) {
            ...
            data2--;
            ...
        }
        ...
    }
}  上面這樣可以分開權限，溫和很多。避免無法取得鎖定時造成的阻斷
```

- 阻斷，也就是執行序被變成等待狀態

## DeadLock

- `p1123` 

- 這裡面有描述 文字描述也不錯，直接看應該能理解的

## Cachesyn的問題

- `p1129` 

- 這邊有提到 如果當run 的條件沒有volatile 並且while中內容使用或者不使用 System.out.print會影響 快取、同步機制 ，isContinue就會因為使用System.out.print <font style="color:lightgreen">被系統要求同步</font>.。

- 在 Java 中，`System.out.println` 通常在輸出訊息時會調用系統的 `flush()` 方法，這會刷新輸出流並確保輸出被寫入控制台。在這個過程中，由於輸出流的特性，可能會引發一些底層的線程同步操作。
  
  因此，當執行 `System.out.println("hi");` 時，這個操作可能會導致底層的線程同步，進而導致線程之間的數據同步，使對 `isContinue` 的修改對其他線程可見，從而能夠正確地停止線程。
  
  當你移除 `System.out.println("hi");` 這行程式碼時，可能由於缺乏明確的<font style="color:lightgreen">同步點</font>，導致一些線程在執行過程中<font style="color:lightgreen">仍在使用快取中的舊值</font>，從而可能無法及時停止線程。這是因為缺乏明確的同步機制，沒有強制要求線程主動刷新快取。

## AtomicInteger  /əˈtɑː.mɪk/

- `p1130`

- 除了使用Volatile + synchronized可以保證同步以及可見
  
  也能使用AtomicInteger這種物件，他也會確保，而且效率更好，因為他是從硬體基於 CAS（Compare-And-Swap）去保證的。
  
  [JUC原子类: CAS, Unsafe和原子类详解 | Java 全栈知识体系 (pdai.tech)](https://pdai.tech/md/java/thread/java-thread-x-juc-AtomicInteger.html) 源碼解析，可以深入理解也有其他知識。

## Wait / notify

- `p1132_ProducerClerk.java`

- 這裡面使用自創方法
  
  waitIfFull()  、waitIfEmpty () 都在內部 wait()
  
  setProduct、getProduct 則是內部使用notify通知
  
  可以看一下怎麼使用的 !
  
  - `wait`   thread 睡到 notify 通知或者interrupted打擾
  
  - `notify` 通知在該物件等待的thread
  
  - 睡著後起來依舊在迴圈，會`再度判斷`是不是真的有東西了 !
  
  ```java
  private synchronized void waitIfEmpty() throws InterruptedException {
          while (this.product==EMPTY) {
          wait();
      }
  }
  ```

- 當你呼叫 `wait()` 時，它會使得執行緒進入等待狀態，直到其他執行緒呼叫了同一個物件的 `notify()` 方法，或是該執行緒被 `interrupt()` 方法中斷。

## ReentrantLock

- `p1138_ReentrantLock.java`

- `this.lock.tryLock()` 

- `this.lock.isHeldByCurrentThread()` 
  
  上面兩個第二個應該蠻好懂
  
  `tryLock`則是 如果物件沒有被其他執行序上鎖 那我就可以試圖上鎖
  
  如果我上鎖，別人就不能碰，除非我 `unlock` 

- 也可以看出其實失敗蠻多次，因為要一直try跟unlock
  
  有提到去看`p1123` => 就是互相佔有互相等待`deadlock`
  
  關於這個目前1138提供就是ReentrantLock
  
  也可以考慮用`semaphore` 
  
  <font style="color:lightgreen">特別創造p1123_deadlock_semaphore.java來示範 semaphore</font>
  
  11/26/2023 勉強弄出來，弄兩種方式
  
  `synchronized(lock1 、lock2)` 的方式去做

- <font style="color:lightgreen">幾乎跟synchronized差不多，比較需要注意的是finally要釋放</font> 
  
  - Lock介面的唯一實現類，都以reentrant命名。
    
    - ReentrantLock
    
    - ReentrantReadWriteLock.ReadLock
    
    - ReentrantReadWriteLock.WriteLock
  
  - 基本上 `syn` 跟 `reentrant` 獲取同一個鎖都不需要阻塞
  
  - 大部分形況兩人性能差不多，但後者要注意finally 釋放鎖避免死鎖。
