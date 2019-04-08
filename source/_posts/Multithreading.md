title: Multithreading in Java ——Synchronized

date: 2018-11-14 14:16:00

categories: Java

tags: 

- java 
- multithreading

---

Thread safe is the key point in multithreading area. The reasons for multithreading problems are mainly on that: 1. Sharing resource/data 2. more than one threads operate the same data/resource. So in order to solve this problem, we need some technique to make sure only one thread operate the sharing data at the same time. In java, **synchronized** key word could work as we expected. Now we try to figure out how thie key word works.

### Three ways to use synchronized key word

The *synchronized* key word could be used in three ways. 

1. In instance method
2. In static method
3. In code block 

```java
class ThreadDemo {
    // sharing data
    static int counter = 0;
    // In instance method
    public synchronized void add(){
        counter++;
    }
    // In static method
    public static synchronized void delete(int n){
        counter -= n;
    }
    // In code block 
    public void halve(){
        synchronized(this) {
            if (counter % 2 == 0) {
                counter *= 2;
            } 
        }
    }
}
```

### The principle of synchronized

As we know java is a Objected-Oriented language. Everything in java is object. The class is actually an object, we call it class object. Everytime we use synchronized key word, the thread will try to get the object lock before execute the relevant code. The thread which gain the object lock will execute the code, others will keep waiting until gain the lock. This then could make sure no multithread safe problems. 

Now we talk about the object lock. In java, every object has three parts in memory: object head, instance variable, padding data. The object lock are based on the object head and the monitor object.

#### object head

JVM use 2 byte to save object head. (Array object will has 3 byte, the additional one is used to save the length of array). The first byte is called *Mark Word*. It usually contains hashcode, age of object, lock info.

| lock status | 25 bit   | 4 bit | 1 bit             | 2 bit lock flag bit |
| ----------- | -------- | ----- | ----------------- | ------------------- |
| no lock     | Hashcode | Age   | Is biased locking | 01                  |

| Lightweight locking | Pointer to lock record in stack | 00   |
| ------------------- | ------------------------------- | ---- |
| Heavyweight locking | pointer to mutex lock           | 10   |
| GC sign             | None                            | 11   |

| biased locking | thread ID(23 bit) | Epoch(2 bit) | age  | 1    | 01   |
| -------------- | ----------------- | ------------ | ---- | ---- | ---- |
|                |                   |              |      |      |      |

In the Heavyweight locking, which is usually the object lock *synchronized* key word used, the pointer in object head points to monitor of current object. Every object has a monitor. Different JVM implement the monitor in different ways. But the basic idea are common. The *synchronized* first read from object head to find the pointer of monitor then use the monitor as the lock to keep the multithread program safe. The wait()/ notify()/ notifyAll() method which are in the Object class  keep the multithread safe in the same way.

There are little difference between the three ways in the low level. 

*In code block* way use **monitorenter** and **monitorexit** instruction directly. The other two ways set ACC_SYNCHRONIZED flag on the method_info Structure in JVM method constants pool. And every time execute the method, thread will first check this flag and if it's settled, thread will try to gain the monitor first.

By the way, **the monitor lock is reentrant**. If a method gain the lock, it could also gain the lock in this method. 

### Optimsization of Synchronized 

#### biased locking

Sometimes the lock is requested by the same thread. In biased locking status, no other synchronize operation need to do. If this fail, the lock will turn into Lightweight locking.

#### Lightweight locking

This locking are based on the idea that in most time, the thread will keep the lock and finish the task without other thread request the same lock. No competition happens. If this fail, the lock will turn into Spin lock.

#### Spin lock

Before turn into Heavyweight locking, JVM will spin for some time in order to gain the lock, if thread could gain the lock at this time, it will not hang the thread in OS  level which could save much time. 

#### Lock Elision

No meaning locks will be deleted to save time. 