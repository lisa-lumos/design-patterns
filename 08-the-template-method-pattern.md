# 8. The template method pattern
When two classes are similar to each other: 
- The Coffee class has methods like boil water, brew coffee, pour into cup, add sugar and milk
- The Tea class has methods like boil water, add tea to water, pour into cup, add lemon

Note that both class has two identical methods: boil water, and pour into cup. Current implementation causes code duplication, which we should avoid. 

The other two not identical methods can be abstracted to: use hot water to extract the coffee or tea, and add the appropriate condiments to the beverage. 

```java
public abstract class CaffeineBeverage {
  // the template method
  final void prepareRecipe() { 
    boilWater(); 
    brew(); 
    pourInCup(); 
    addCondiments(); 
  }

  abstract void brew();
  abstract void addCondiments();

  void boilWater() { 
    System.out.println("Boiling water"); 
  }

  void pourInCup() { 
    System.out.println("Pouring into cup"); 
  }
}

public class Tea extends CaffeineBeverage {
  public void brew() { 
    System.out.println("Steeping the tea"); 
  } 
  public void addCondiments() {
    System.out.println("Adding Lemon"); 
  }
}

public class Coffee extends CaffeineBeverage {
  public void brew() { 
    System.out.println("Dripping Coffee through filter"); 
  } 
  public void addCondiments() {
    System.out.println("Adding Sugar and Milk"); 
  }
}
```

The Template Method defines the steps of an algorithm, and allows subclasses to provide the implementation for one or more steps.

The Template Method Pattern defines the skeleton of an algorithm in a method, deferring some steps to subclasses. Template Method lets subclasses redefine certain steps of an algorithm, without changing the algorithm's structure.

A hook is a method that is declared in the abstract class, but only given an empty or default implementation. This gives subclasses the ability to "hook into" the algorithm at various points, if they wish; a subclass is also free to ignore the hook. 

```java
public abstract class CaffeineBeverageWithHook {

  final void prepareRecipe() {
    boilWater(); 
    brew(); 
    pourInCup(); 
    if (customerWantsCondiments()) { 
      addCondiments(); 
    }
  }

  abstract void brew();
  abstract void addCondiments();

  void boilWater() { 
    System.out.println("Boiling water"); 
  }

  void pourInCup() { 
    System.out.println("Pouring into cup");
  }

  boolean customerWantsCondiments() { 
    return true; 
  }
}

// To use the hook, we override it in our subclass. 
// Here, the hook controls whether the CaffeineBeverage class evaluates a certain part of the algorithm,
// that is, whether it adds a condiment to the beverage.
public class CoffeeWithHook extends CaffeineBeverageWithHook { 
  public void brew() { 
    System.out.println("Dripping Coffee through filter"); 
  } 

  public void addCondiments() { 
    System.out.println("Adding Sugar and Milk"); 
  }

  public boolean customerWantsCondiments() { 
    String answer = getUserInput(); 
    if (answer.toLowerCase().startsWith("y")) { 
      return true; 
    } else { 
      return false; 
    }
  } 
  
  private String getUserInput() { 
    String answer = null; 
    System.out.print("Would you like milk and sugar with your coffee (y/n)? "); 
    BufferedReader in = new BufferedReader(new InputStreamReader(System.in)); 
    try { 
      answer = in.readLine(); 
    } catch (IOException ioe) { 
      System.err.println("IO error trying to read your answer"); 
    } 

    if (answer == null) { 
      return "no"; 
    } 
    return answer;
  }
}

public class BeverageTestDrive { 
  public static void main(String[] args) {
    TeaWithHook teaHook = new TeaWithHook(); 
    CoffeeWithHook coffeeHook = new CoffeeWithHook();
    System.out.println("\nMaking tea..."); 
    teaHook.prepareRecipe();
    System.out.println("\nMaking coffee..."); 
    coffeeHook.prepareRecipe();
  }
}
```

Use abstract methods when your subclass MUST provide an implementation of the method. Use hooks when that part of the algorithm is optional. With hooks, a subclass may choose to implement that hook, but it doesn't have to.

The Hollywood Principle: Don't call us, we'll call you.

The Hollywood Principle gives us a way to prevent "dependency rot". Dependency rot happens when you have high-level components (such as abstract classes) depending on low-level components (such as concrete classes) depending on high-level components depending on sideways components depending on low-level components, and so on. When rot sets in, no one can easily understand the way a system is designed. With the Hollywood Principle, we allow low-level components to hook themselves into a system, but the high-level components determine when they are needed, and how. In other words, the high-level components give the low-level components the "don't call us, we'll call you" treatment.

When we design with the Template Method Pattern, we're telling subclasses, "don't call us, we'll call you". Tea and Coffee concrete classes are used simply to provide implementation details, they never call the abstract class directly without being "called" first. The high-level abstract component calls on the subclasses only, when they are needed for an implementation of a method. 
