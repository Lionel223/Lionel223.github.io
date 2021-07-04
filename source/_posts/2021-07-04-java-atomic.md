---
title: java_atomic
date: 2021-07-04 21:36:48
tags: ["java", "atomic"]
categories: java
---

在 JAVA 裡面，有些 Class 的前綴詞是以`Atomic` 為開頭的，本文主要介紹 Atomic 的意義以及使用方式。

<!-- more -->

## Atomic 的意義

在電腦領域中，Atomic 的意思是不可再分割的意思，例如多個 thread 在處理一個全域的資源時，Atomic 的操作可以確保同一個時刻只有唯一的 thread 在對這個資源進行處理。

Atomic Operation 表示不會被打斷的操作，它可以是一個步驟，也可以是多個步驟，但其順序不可被打亂，且不可被切割，只執行其中一部份。

## JAVA 中的 Atomic

在 JAVA 也有一個 package 名為 `java.util.concurrent.atomic`，這個 package 底下的 class 皆提供了防鎖死（lock-free），以及執行緒安全（thread-safe）的 Atomic Operation 方法，以下簡易介紹一下 lock-free 以及 thread-safe

### lock-free

lock-free 的意義是當 A thread 正在使用 Atomic 的 method 時，B thread 此時去存取會被即時拒絕，所以 B thread 不會被鎖死。為了達成 lock-free，他們使用了 lock and wait-free algorithms（Nonblocking algorithms）以及 CPU 中的 CAS（Compare and Swap）

### thread-safe

當我們說這個 Class 是 thread-safe 時，表示當他被多個 thread 使用時，Class 可以持續進行正確的行為，不須考慮 thread 在運行情況下的交替執行，也不需做額外的同步機制。例如以下的 Class 就不是 thread safe的。

```java
public class Counter {
    private int number = 0;
    public int add() {
        return number++; // 不是thread-safe，因為相當於number = number + 1;
    }
}
```

`number++` 相當於 `number=number+ 1`，需要先從變數 `number` 取出值再加一，然後存入 `number` 變數中，在 multi thread 時這個步驟可能會遇到取到中間值的情況，例如當兩個 thread 同時使用 add method 時，可能導致結果是 1 或是 2。

## JAVA 的 Atmoic Class 以及 method

`java.util.concurrent.atomic` 裡面的 class 的開頭都有 Atomic。底下是 atomic 的 class

- AtomicBoolean
- AtomicInteger
- AtomicIntegerArray
- AtomicIntegerFieldUpdater
- AtomicLong
- AtomicLongArray
- AtomicLongFieldUpdater
- AtomicReference

以前面的 Counter Class 為例，我們在這邊就可以使用 `AtomicInteger` 來實踐剛剛的方法

```java
import java.util.concurrent.atomic.AtomicInteger;

public class AtomicCounter {
    private AtomicInteger atomicNumber = new AtomicInteger();
    public int add() {
        return atomicNumber.getAndIncrement(); // thread-safe
    }
}
```

此處我們使用了 method `getAndIncrement`，他可以對原本的數值加一並且返回原先的數值，底下再介紹數個 `AtomicInteger` 的 method

* compareAndSet

  當輸入的值符合預期值（expect），就將值更新上去

  ```java
  public final boolean compareAndSet(int expect, int update)
  ```

  定義：Atomically sets the value to the given updated value if the current value `==` the expected value.

  - **Parameters**

    `expect` - the expected value

    `update` - the new value

  - **Returns**

    `true` if successful. False return indicates that the actual value was not equal to the expected value.

  - **Example**

    ```java
    import java.util.concurrent.atomic.AtomicInteger; 
      
    public class Test 
    { 
        public static void main(String[] args) 
        {
            // create an atomic integer object. 
            AtomicInteger<Integer> atomicNumber = new AtomicInteger<Integer>(); 
            // set some value 
            ref.set(1); 
            // 輸入的expect是2，但目前的值是1，所以不會更新，且response為false
            boolean response = ref.compareAndSet(2, 3); 
      
            // print value 
            System.out.println(" Value is set = " + response); 
        } 
    }
    ```

    **Output**

    ```
    Value is set = false
    ```

* getAndAccumulate

  返回先前的值，並使用自定義的運算來更新當前值

  ```java
  public final int getAndAccumulate(int x, IntBinaryOperator accumulatorFunction)
  ```

  Atomically updates the current value with the results of applying the given function to the current and given values, returning the previous value. The function should be side-effect-free, since it may be re-applied when attempted updates fail due to contention among threads. The function is applied with the current value as its first argument, and the given update as the second argument.

  - **Parameters**

    `x` - the update value

    `accumulatorFunction` - a side-effect-free function of two arguments

  - **Returns**

    the previous value

  - **Example**

    ```java
    import java.util.concurrent.atomic.AtomicInteger; 
    import java.util.function.IntBinaryOperator; 
      
    public class Test { 
        public static void main(String[] args) 
        { 
            new UserThread("Thread A"); 
            new UserThread("Thread B"); 
        } 
    } 
      
    class Shared { 
        static AtomicInteger ai = new AtomicInteger(1); 
    } 
      
    class UserThread implements Runnable { 
        String name; 
      
        UserThread(String name) 
        { 
            this.name = name; 
            new Thread(this).start(); 
        } 
      
        IntBinaryOperator ibo = (x, y) -> (x * y); // x是原先的值，y是下面的value
      
        int value = 5; 
        @Override
        public void run() 
        { 
            for (int i = 0; i < 3; i++) { 
      
                int ans = Shared.ai.getAndAccumulate(value, ibo); 
                System.out.println(name + " " + ans); 
            } 
        } 
    }
    ```

    **Output**

    ```
    Thread A 1
    Thread A 5
    Thread A 25
    Thread B 125
    Thread B 625
    Thread B 3125
    ```

* getAndUpdate

  返回先前的值，並更新為指定值

  ```java
  public final int getAndUpdate(IntUnaryOperator updateFunction)
  ```

  Atomically updates the current value with the results of applying the given function, returning the previous value. The function should be side-effect-free, since it may be re-applied when attempted updates fail due to contention among threads.

  - **Parameters:**

    `updateFunction` - a side-effect-free function

  - **Returns:**

    the previous value

  - **Example**

    ```java
    import java.util.concurrent.atomic.AtomicInteger; 
    import java.util.function.IntBinaryOperator; 
      
    public class Test { 
        public static void main(String[] args) 
        { 
            AtomicInteger ai = new AtomicInteger(0);
    	      int threadInt = ...
            
            // Update ai atomically, but only if the current value is less than threadInt
    				ai.updateAndGet(value -> value < threadInt ? threadInt : value);
        } 
    } 
    ```

  ※ `GetAndAccumulate` 與 `GetAndupdate` 都很相似，從 implementation 來看的話，會發現兩者只有一點點差異，`GetAndupdate` 算是更基本的操作，而 `GetAndAccumulate` 則是較快捷的方式

## Reference

* [What does “atomic” mean in programming?](https://stackoverflow.com/questions/15054086/what-does-atomic-mean-in-programming)
* [Java java.util.concurrent.atomic.AtomicInteger用途](https://matthung0807.blogspot.com/2019/05/java-javautilconcurrentatomicatomicinte.html)
* [[Java] Atomic的介紹](https://ithelp.ithome.com.tw/articles/10229833)
* [原子操作](https://baike.baidu.com/item/%E5%8E%9F%E5%AD%90%E6%93%8D%E4%BD%9C)
* [Package java.util.concurrent.atomic](https://docs.oracle.com/javase/8/docs/api/index.html?java/util/concurrent/atomic/package-summary.html)
* [AtomicInteger getAndAccumulate() method in Java with Examples](https://www.geeksforgeeks.org/atomicinteger-getandaccumulate-method-in-java-with-examples/)
* [How to update an Atomic based on a condition?](https://stackoverflow.com/questions/29626524/how-to-update-an-atomic-based-on-a-condition)
* [Is there any functional difference between AtomicInteger.updateAndGet() and AtomicInteger.accumulateAndGet()?](https://stackoverflow.com/questions/36022739/is-there-any-functional-difference-between-atomicinteger-updateandget-and-atom)

