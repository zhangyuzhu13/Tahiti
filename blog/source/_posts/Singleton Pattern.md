title: Singleton Pattern

date: 2018-11-13 14:16:00

categories: System Design

tags:  singleton

---

Singleton Pattern is a kind of design pattern which used to keep a object unique. For every user who want to use this class to create a object, he should get the same one. Singleton pattern could be used in many scenarios:

* In order to control resource, database connecting objects or I/O objects are usually created in singleton pattern
* In order to share resource, log file, app config

The advantages of singleton pattern are:

* Save memory because only one instance in memory
* Make sure all objects visit the same instance.

The disadvantages are:

* Not suitable for unstable objects
* Hard to extend
* Too much responsibility 

There are many ways to implement the singleton pattern.

1. Lazy init without thread safe

   ```java
   public class Singleton {
       private static Singleton instance;
       private Singleton(){}
       
       public static Singleton getInstance(){
           if (instance == null) {
               instance == new Singleton();
           }
           return instance;
       }
   }
   ```


2. Lazy init with thread safe

   ```java
   public class Singleton {
       private static Singleton instance;
       private Singleton(){}
       
       public static synchronized Singleton getInstance(){
           if (instance == null) {
               instance == new Singleton();
           }
           return instance;
       }
   }
   ```


3. double-check locking

   ```java
   public class Singleton {
       private static Singleton instance;
       private Singleton(){}
       
       public static Singleton getInstance(){
           if (instance == null) {
               synchronized(Singleton.class) {
                   if (instance == null) {
                       instance == new Singleton();
                   }
               }
           }
           return instance;
       }
   }
   ```

4. Eager instantiation

   ```java
   public class Singleton {
       private static Singleton instance = new Singleton();
       private Singleton(){}
       
       public static Singleton getInstance() {
           return instance;
       }
   }
   ```

5. static inner class based on the classLoader rules

   ```java
   public class Singleton {
       private static class SingletonHolder {
           /* when load Singleton class, SingletonHolder won't be load without 
           * directly use of SingletonHolder class. That's how this way 
           * could achieve lazy load.
           */
           private static final Singleton INSTANCE = new Singleton();
       }
       private Singleton(){}
       
       public static Singleton getInstance() {
           return SingletonHolder.INSTANCE;
       }
   }
   ```

6. Method 5 is good when thread safe, but not good enough when singleton are used in serialization and deserialization. It would create more than one instances. We could change it that way.

   ```java
   public class Singleton implements Serializable {
       private static final long serializationID = 888L;
       private static class SingletonHolder {
           private static final Singleton INSTANCE = new Singleton();
       }
       private Singleton(){}
       
       public static Singleton getInstance() {
           return SingletonHolder.INSTANCE;
       }
       /* This method is from Serializable interface
       */
       protected Singleton readResolve() throws ObjectStreamException {
           return SingletonHolder.INSTANCE;
       }
   }
   ```

   *readResolve() is used for replacing the object read from the stream. The only use I've ever seen for this is enforcing singletons; when an object is read, replace it with the singleton instance. This ensures that nobody can create another instance by serializing and deserializing the singleton.* —— from stackoverflow

7. static block would be executed when class is used. So this character could be used to implement singleton pattern.

   ```java
   public class Singleton {
       private static Singleton instance = ;
       private Singleton(){}
       static {
           instance = new Singleton();
       }
       public static Singleton getInstance() {
           return instance;
       }
   }
   ```

8. Enum class just has the same character with static block. So we also could use enum class to implement it. The advantage of  using enum to implement are thread safe and free serialization. It's very convenient.

   ```java
   public Enum Singleton {
       INSTANCE;
       public void whateverMethod(){}
   }
   ```

Usually we should use eager instantiation. When lazy load needed, we could use the static inner class. About serialization safe, enum is a much better choice.