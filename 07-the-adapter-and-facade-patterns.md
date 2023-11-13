# 7. The Adapter and Facade Patterns
## The Adapter Pattern
OO adapters take an interface, and adapt it to one that a client is expecting.

Say you've got an existing software system that you need to work a new vendor class library into, but the new vendor designed their interfaces differently than the last vendor. You don't want to solve the problem by changing your existing code, and you can't change the vendor's code. So, you can write a class that adapts the new vendor interface into the one you're expecting. The adapter acts as the middleman, by receiving requests from the client, and converting them into requests that make sense on the vendor classes.

For example, the Duck interface:
```java
public interface Duck { 
  public void quack(); 
  public void fly(); 
}
```

A MallardDuck class:
```java
public class MallardDuck implements Duck { 
  public void quack() { 
    System.out.println("Quack"); 
  }

  public void fly() { 
    System.out.println("I'm flying"); 
  }
}
```

But the new fowl interface:
```java
public interface Turkey { 
  public void gobble(); 
  public void fly(); 
}
```

And the WildTurkey class:
```java
public class WildTurkey implements Turkey {
  public void gobble() { 
    System.out.println("Gobble gobble"); 
  }
  public void fly() { 
    System.out.println("I'm flying a short distance"); 
  }
}
```

Now, assume you're short on Duck objects, and you'd like to use some Turkey objects in their place. Obviously we can't use the turkeys directly, because they have a different interface. So, we would need to write an Adapter:
```java
public class TurkeyAdapter implements Duck { 
  Turkey turkey;

  public TurkeyAdapter(Turkey turkey) { 
    this.turkey = turkey; 
  }

  public void quack() { 
    turkey.gobble(); 
  }

  public void fly() { 
    for(int i = 0; i < 5; i++) { 
      turkey.fly(); 
    } 
  }
}
```

And the main:
```java
public class DuckTestDrive {
  public static void main(String[] args) { 
    Duck duck = new MallardDuck();

    Turkey turkey = new WildTurkey(); 
    Duck turkeyAdapter = new TurkeyAdapter(turkey); // the turkey now looks like a duck

    System.out.println("The Turkey says..."); 
    turkey.gobble(); 
    turkey.fly();

    System.out.println("\nThe Duck says..."); 
    testDuck(duck);

    System.out.println("\nThe TurkeyAdapter says..."); 
    testDuck(turkeyAdapter);
  }

  static void testDuck(Duck duck) { 
    duck.quack(); 
    duck.fly(); 
  }
}
```

The Adapter Pattern converts the interface of a class into another interface the clients expect. Adapter lets classes work together that couldn't otherwise, because of incompatible interfaces.

This pattern acts to decouple the client from the implemented interface, and if we expect the interface to change over time, the adapter encapsulates that change, so that the client doesn't have to be modified each time it needs to operate against a different interface.

There are 2 kinds of adapters: object adapters, and class adapters.

To implement a class adapter, you need multiple inheritance to implement it, so it is impossible in Java.

the Adapter Pattern converts the interface of a class into one that a client is expecting. We achieve this in Java, by wrapping the object that has an incompatible interface with an object that implements the correct one.

## The Facade Pattern
The Facade Pattern makes an interface simpler. 

With the Facade Pattern, you can take a complex subsystem, and make it easier to use, by implementing a Facade class ,that provides one, more reasonable interface. If you need the power of the complex subsystem, it's still there for you to use, but if all you need is a straightforward interface, the Facade is there for you.

The Facade Pattern also allows you to decouple your client implementation from any one subsystem. Assume you decide to upgrade your home theater to all new components that have different interfaces. If you coded your client to the facade, rather than the subsystem, your client code doesn't need to change, just the facade.










