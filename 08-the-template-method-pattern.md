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


























