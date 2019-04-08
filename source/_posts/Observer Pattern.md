title: Observer Pattern

date: 2018-11-19 14:16:00

categories: System Design

tags:  observer pattern

---

Observer Pattern(other names as Publish/Subscribe, Model/View) is used to build a one-on-one or one-on-many relation between objects. When the 'one' object happens to change, other objects could get announcement and then make their own update. The object which happens to change is called subject, the objects which get announcement are called observer. There are no relationship between observers and we could easily add and delete observers without changing other observers. Thus system could be more flexible to extend.

### UML for Observer Pattern

![Observer Pattern UML](/blogimg/ObserverUML.png)

### Advantages

* **Split the model level and the view level, define reliable messaging way, abstract the update interface and could create different types of observers**
* Abstract coupling between subject and Observer
* Support broadcast
* Follow the "Open-Close" principle

### Disadvantages

* It may cost much to announce all the observers
* Observers may have cycle relation thus crush system
* Don't know how the subject change

### Scenario 

* A object could affect B object and don't know how many other objects like b would be influenced.
* A affect B and B affect C 

### Implementation

```java
import java.util.List;
import java.util.Observer;

public class MySubject {
    private List<Observer> list;
    private int state = 0;
    public void attach(Observer observer) {
        list.add(observer);
    }
    public void detach(Observer observer) {
        for (Observer obr : list) {
            if (obr == observer) {
                list.remove(obr);
                return;
            }
        }
    }
    public void announce() {
        for (Observer obr : list) {
            obr.update(this);
        }
    }
    public int getState() {
        return state;
    }
    public void setState(int state) {
        this.state = state;   
    }
}
```

```java
public interface Observer {
    void update(Object arg);
}
```

```java
public class MySubject implements Observer{
    private int observerState = 0;
    @Override
    public void update(MySubject subject) {
        observerState = subject.getState();
        System.out.println(observerState);
    }
}
```



