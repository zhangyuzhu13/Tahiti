title: Factory Pattern

date: 2018-11-15 14:16:00

categories: System Design

tags:  factory

---

Factory patterns are widely used in Software Development. There are three type of factory patterns. They are *Simple Factory Pattern*, *Factory Pattern*, *Abstract Factory Pattern*. We will introduce these three pattern.

### Simple Factory Pattern

There is only a factory class which used to create simple object.

Scenario: The objects needed to create is simple and not too much.

Example: we have many fruits to get. Those fruits have the same interface.

```java
public interface Fruit{}
class Apple implements Fruit{}
class Bear implements Fruit{}
class StrawBerry implements Fruit{}
```

Then we have a factory to get fruits.

```java
public class FruitFactory {
    public getFruit(String fruit) {
        Fruit instance;
        if ("apple".equals(fruit)) {
            instance = new Apple();
        }
        if ("bear".equals(fruit)) {
            instance = new Bear();
        }
        if ("strawberry".equals(fruit)) {
            instance = new StrawBerry();
        }
        return instance;
    }
}
```

Now we could use this factory like this.

```java
FruitFactory factory = new FruitFactory();
Fruit fruit = factory.getFruit("apple");
```

Just according to different parameters we could get different fruit objects.



### Factory Pattern

This pattern would be more complex. We need to abstract the factory and implement different factory for different objects.

Scenario: This pattern could be used when we don't need to know or care about each type of object which would created by factory. 

Now we want to implement a hiring class that could find different kind of worker to do the job.

First we have a worker interface and several kind of worker class

```java
public interface Worker {
    public void work();
}
class Teacher implements Worker {
    public void work() {
        System.out.println("I teach");
    }
}
class Driver implements Worker {
    public void work() {
        System.out.println("I drive");
    }
}
```

Then we could implement a abstract factory class and each worker should have a factory class.

```java
public interface WorkerFactory {
    public void hire();
}
class TeacherFactory implements WorkerFactory {
    public void hire() {
        return new Teacher();
    }
}
class DriverFactory implements WorkerFactory {
    public void hire() {
        return new Driver();
    }
}
```

Now we could use this factory to hire some workers to work.

```java
WorkerFactory factory = new TeacherFactory();
WOrker worker = factory.hire();
worker.work();
```

### Abstract Factory Pattern

This pattern is the most complex one and it violate the open-close principle. So be more careful when using this pattern.

Abstract Factory pattern would be used when we need to create more than one object and use them to work together for one feature. What's more important is that this part should be stable and won't change many often. That's the key point.

Now we have two supermarkets where we could get drink and food. We want to use abstract factory to implement this.

```java
public interface DrinkProvider{
    public void provideDrink();
}
public interface FoodProvider{
    public void provideDood();
}
class Coca implements DrinkProvider {
    public void provideDrink() {
        System.out.println("give you some Coca");
    }
}
class Pepsi implements DrinkProvider {
    public void provideDrink() {
        System.out.println("give you some Pepsi");
    }
}
class Domino implements FoodProvider {
    public void provideDood() {
        System.out.println("give you some pizza from Domino");
    }
}
class Pizzahut implements FoodProvider {
    public void provideDood() {
        System.out.println("give you some pizza from Pizzahut");
    }
}

```

Now we use abstract factory pattern to create two supermarket where we could get food and drink of different kinds.

```java
public interface SuperMarket {
    public FoodProvider getFoodProvider();
    public DrinkProvider getDrinkProvider();
}
class Walmart implements SuperMarket {
    public FoodProvider getFoodProvider() {
        return new Domino();
    }
    public DrinkProvider getDrinkProvider() {
        return new Coca();
    }
}
class Ingles implements SuperMarket {
    public FoodProvider getFoodProvider() {
        return new Pizzahut();
    }
    public DrinkProvider getDrinkProvider() {
        return new Pepsi();
    }
}
```

Then we could use factories like this.

```java
SuperMarket shop = new Walmart();
FoodProvider foodProvider = shop.getFoodProvider();
DrinkProvider drinkProvider = shop.getDrinkProvider();
foodProvider.provideDood();
drinkProvider.provideDrink();
```

