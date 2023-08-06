# 5. The Singleton Pattern
The Singleton Pattern is a convention, for ensuring one and only one object is instantiated, for a given class.

There are many objects we only need one of: thread pools, caches, dialog boxes, objects that handle preferences and registry settings, objects used for logging, and objects that act as device drivers to devices like printers and graphics cards. In fact, for many of these types of objects, if we were to instantiate more than one, we'd run into problems, like incorrect program behavior, overuse of resources, or inconsistent results.

First draft of the Singleton class:
```java
public class Singleton {

  private static Singleton uniqueInstance; // static var to hold one instance
  // ... other useful instance variables 

  private Singleton() {} // only the class itself can instantiate the class

  public static Singleton getInstance() {
    if (uniqueInstance == null) { // the class itself hold its only instance, as a static var
      uniqueInstance = new Singleton();
    }
    return uniqueInstance; 
  } 

  // ... other useful methods
}
```

The Singleton pattern ensures that every object in your app is making use of the same global resource.

Take a chocolate boiler controller example, which controls a chocolate boiler in a factory, the below code tried very hard to ensure bad things won't happen:
```java
public class ChocolateBoiler { 
  private boolean empty; 
  private boolean boiled;

  public ChocolateBoiler() { 
    empty = true; 
    boiled = false; 
  }

  public void fill() {
    if (isEmpty()) { 
      empty = false; 
      boiled = false; 
      // then fill the boiler with a milk/chocolate mixture 
    } 
  }

  public void drain() {
    if (!isEmpty() && isBoiled()) { 
      // first drain the boiled milk and chocolate, then
      empty = true; 
    } 
  }

  public void boil() {
    if (!isEmpty() && !isBoiled()) { 
      // first bring the contents to a boil 
      boiled = true; 
    } 
  }

  public boolean isEmpty() {
    return empty; 
  }

  public boolean isBoiled() {
    return boiled; 
  }
}
```

But if more than one instance of ChocolateBoiler is created in an app, they many not agree on the status of the boiler. This would cause confusion. 

The Singleton Pattern ensures a class has only one instance, and provides a global point of access to it.

We can implement this so that the Singleton is created in a lazy manner, which is especially important for resource-intensive objects.

The problem with the first draft of the Singleton class, is that it cannot handle multithreading - as multiple threads can call the getInstance() synchronously. 

To solve the problem, we can add `synchronized` keyword to this getInstance() method, but synchronization is expensive. The only time synchronization is relevant, is the first time through this method. In other words, once we've set the uniqueInstance variable to an instance of Singleton, we have no further need to synchronize this method. 






















