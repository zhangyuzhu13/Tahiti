title: Prototype Pattern

date: 2018-11-14 14:16:00

categories: System Design

tags:  prototype

---

Prototype Pattern will use object instance to create a new same object instance. So in this way, the two instance has some relationship like this: 

* Object o1 = new Object();
* Object o2 = o1.clone();
* o1 != o2
* o1.getClass() == o2.getClass();
* o2.equals(o1) == true;

There are two type of clone:

1. Shallow copy: only copy the value in the instance. As for the object, they only copy the reference of the object. So it means if you use shallow copy to copy a object, they will share the same reference for those object in them.

2. Deep copy: copy not only the value but also the object in the original instance.


The scenarios for this pattern:

1. When a new object could be created after a expensive operation of database, we could keep this object and use prototype to create.


The advantages of prototype pattern are:

1. Sometimes this method could be more efficient than create a new object

Disadvantages:

1. It's hard to implement the clone method cause you need to care about all of the parent class if they are cloneable

The key for implementation in Java is to implement the clone() method.

```java
public class Book implements Cloneable{
    private String name = "Moon and six pence";
    public Object clone() throws CloneNotSupportedException{
        return super.clone();
    }
}
```

In java, prototype pattern just means implement Cloneable interface and override clone method. Usually prototype pattern will show up with Factory pattern together.