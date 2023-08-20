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

Ways to solve the problem:
1. Do nothing, if its performance isn't critical. Synchronizing a method can decrease performance by a factor of 100, so if a high-traffic part of your code begins using getInstance(), you may have to reconsider.
2. Eagerly create the instance, rather than lazily. Using this approach, we rely on the JVM to create the unique instance of the Singleton, when the class is loaded. The JVM guarantees that the instance will be created, before any thread accesses the static uniqueInstance variable.
3. Use "double-checked locking" to reduce the use of synchronization in getInstance(). First check to see if an instance is created, and if not, synchronize. This way, we only synchronize the first time through. Code as shown below. 
```java
public class Singleton { 
  // The volatile keyword ensures that 
  // multiple threads handle the uniqueInstance variable correctly, 
  // when it is being initialized to the Singleton instance.
  private volatile static Singleton uniqueInstance;

  private Singleton() {}

  public static Singleton getInstance() {
    if (uniqueInstance == null) { // if no instance exists, enter synchronized block
      synchronized (Singleton.class) { 
        if (uniqueInstance == null) { 
          uniqueInstance = new Singleton(); 
        } 
      }
    } 
    return uniqueInstance;
  }
}
```

Technically, instead of singleton, you can just create a class in which all methods and variables are defined as static, if your class is self-contained, and doesn't depend on complex initialization. However, because of the way static initializations are handled in Java, this can get very messy, especially if multiple classes are involved. Often this scenario can result in subtle, hard-to-find bugs involving order of initialization. Unless there is a compelling need to do it, it's far better to stay in the object world.

A common criticism of the Singleton Pattern. The loose coupling principle says to "strive for loosely coupled designs between objects that interact". It's easy for Singletons to violate this principle: if you make a change to the Singleton, you'll likely have to make a change to every object connected to it.

Singletons are meant to be used sparingly.

Many of the problems we've discussed - worrying about synchronization, class loading issues, reflection, and serialization/ deserialization issues - can all be solved by using an enum to create your Singleton:
```java
public enum Singleton { 
  UNIQUE_INSTANCE; 
  // more useful fields here 
} 

public class SingletonClient {
  public static void main(String[] args) { 
    Singleton singleton = Singleton.UNIQUE_INSTANCE; 
    // use the singleton here
  } 
}
```

We went through all that earlier work, with creating a Singleton class, with a getInstance() method, and then synchronizing, etc, so you truly understand how Singleton works. Now that you know, you can go off and use enum, whenever you need a Singleton.

Be careful if you are using multiple class loaders; this could defeat the Singleton implementation and result in multiple instances.
