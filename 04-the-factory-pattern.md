# The Factory Pattern
Object instantiation shouldn't always be done in public, and can often lead to coupling problems.

We want to use abstract types to keep code flexible, but we have to create an instance of a concrete class. And when we have many related concrete classes, often we end up with this:
```java
Duck duck;
if (picnic) { 
  duck = new MallardDuck(); 
} else if (hunting) {
  duck = new DecoyDuck(); 
} else if (inBathTub) {
  duck = new RubberDuck(); 
}
```

Here we have several concrete classes being instantiated, and the decision of which to instantiate is made at runtime, depending on some set of conditions. 

When you see code like this, you know that when it comes time for changes or extensions, you'll have to reopen this code, and examine what needs to be added/deleted. 

Often this kind of code ends up in several parts of the application, making maintenance/updates difficult and error-prone.

## The pizza shop example
We need to take the creation code and move it out into another object that is only going to be concerned with creating pizzas.

Factories handle the details of object creation. Gone are the days when the orderPizza() method needs to know about Greek versus Clam pizzas. Now the orderPizza() method just cares that it gets a pizza that implements the Pizza interface, so that it can call prepare(), bake(), cut(), and box().

```java
public class PizzaStore { 
  SimplePizzaFactory factory;
  public PizzaStore(SimplePizzaFactory factory) { 
    this.factory = factory; 
  }
  public Pizza orderPizza(String type) { 
    Pizza pizza;
    pizza = factory.createPizza(type);
    pizza.prepare(); 
    pizza.bake(); 
    pizza.cut(); 
    pizza.box();
    return pizza;
  }
  // other methods here
}

public class SimplePizzaFactory {
  public Pizza createPizza(String type) {
    Pizza pizza = null; 
    if (type.equals("cheese")) {
      pizza = new CheesePizza(); 
    } else if (type.equals("pepperoni")) {
      pizza = new PepperoniPizza(); 
    } else if (type.equals("clam")) {
      pizza = new ClamPizza(); 
    } else if (type.equals("veggie")) {
      pizza = new VeggiePizza(); 
    } 
    return pizza;
  }
}
```

The SimplePizzaFactory class may have many clients, including orderPizza() method. So, by encapsulating the pizza creation in one class, we now have only one place to make modifications when the implementation changes. We also remove the concrete instantiations from our client code.

The Simple Factory is't actually a Design Pattern; it's more of a programming idiom.

In design patterns, "implement an interface" does NOT always mean literally using the implements keyword in the class declaration. A concrete class implementing a method from a supertype (which could be a abstract class/interface) is still considered to be "implementing the interface" of that supertype.

## The pizza store expands
Now assume the pizza store expands to a chain a stores. Each franchise want to offer different styles of pizzas, depending on location.

If we take out SimplePizzaFactory, and create three different factories - NYPizzaFactory, ChicagoPizzaFactory, and CaliforniaPizzaFactory - then we can just compose the PizzaStore with the appropriate factory, and a franchise is good to go.

But, what if you want to create a framework, that ties the store and the pizza creation together, yet still allows things to remain flexible.

What we are going to do is, make the PizzaStore class abstract, put createPizza() method into PizzaStore, and make this method abstract. Then we can create a concrete PizzaStore subclass for each regional style:
```java
public abstract class PizzaStore {
  public Pizza orderPizza(String type) { // this part is fixed
    Pizza pizza;
    pizza = createPizza(type);
    pizza.prepare(); 
    pizza.bake(); 
    pizza.cut(); 
    pizza.box();
    return pizza;
  }
  abstract Pizza createPizza(String type); // this part is flexible
}
```










