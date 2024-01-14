# 9. The Iterator and Composite Patterns
Assume two restaurants are going to have a merge. They both have the same implementation for the menu items, but the menu is implemented differently - one used an ArrayList, and the other used an Array. Neither of them can change their implementations - they have too much code written that depends on them.

Their common menu item implementation:
```java
public class MenuItem { 
  String name; 
  String description; 
  boolean vegetarian; 
  double price;

  public MenuItem(String name, String description, boolean vegetarian, double price) {
    this.name = name;
    this.description = description;
    this.vegetarian = vegetarian; 
    this.price = price; 
  }

  public String getName() { 
    return name; 
  }

  public String getDescription() { 
    return description; 
  }

  public double getPrice() { 
    return price; 
  }

  public boolean isVegetarian() { 
    return vegetarian; 
  }
}
```

Restaurant 1 has this ArrayList menu implementation:
```java
public class PancakeHouseMenu { 
  List<MenuItem> menuItems;

  public PancakeHouseMenu() { 
    menuItems = new ArrayList<MenuItem>();

    addItem("K&B's Pancake Breakfast", "Pancakes with scrambled eggs and toast", true, 2.99);
    addItem("Regular Pancake Breakfast", "Pancakes with fried eggs, sausage", false, 2.99);
    addItem("Blueberry Pancakes", "Pancakes made with fresh blueberries", true, 3.49);
    addItem("Waffles", "Waffles with your choice of blueberries or strawberries", true, 3.59);
  }

  public void addItem(String name, String description, boolean vegetarian, double price) { 
    MenuItem menuItem = new MenuItem(name, description, vegetarian, price); 
    menuItems.add(menuItem); 
  }

  public ArrayList<MenuItem> getMenuItems() { 
    return menuItems; 
  }

  // other menu methods here
}
```

Restaurant 2 has this Array menu implementation:
```java
public class DinerMenu {
  static final int MAX_ITEMS = 6; 
  int numberOfItems = 0; 
  MenuItem[] menuItems;

  public DinerMenu() { 
    menuItems = new MenuItem[MAX_ITEMS];
    addItem("Vegetarian BLT", "(Fakin') Bacon with lettuce & tomato on whole wheat", true, 2.99); 
    addItem("BLT", "Bacon with lettuce & tomato on whole wheat", false, 2.99); 
    addItem("Soup of the day", "Soup of the day, with a side of potato salad", false, 3.29); 
    addItem("Hotdog", "A hot dog, with sauerkraut, relish, onions, topped with cheese", false, 3.05); 
    // a couple of other Diner Menu items added here
  }

  public void addItem(String name, String description, boolean vegetarian, double price) { 
    MenuItem menuItem = new MenuItem(name, description, vegetarian, price); 
    if (numberOfItems >= MAX_ITEMS) { 
      System.err.println("Sorry, menu is full! Can't add item to menu"); 
    } else { 
      menuItems[numberOfItems] = menuItem; 
      numberOfItems = numberOfItems + 1; 
    }
  }

  public MenuItem[] getMenuItems() { 
    return menuItems; 
  }

  // other menu methods here
}
```

Assume after the merge, you need to be able to print the whole menu, breakfast/lunch menus, vegetarian menus, etc. 

To implement every method, we always going to need to get both menus and use two loops to iterate through their items. If another restaurant with a different implementation is acquired, then we'll have three loops.

It would be nice, if we could find a way to allow them to implement the same interface for their menus (they're already close, except for the return type of the getMenuItems() method). That way, we can minimize the concrete references in the Waitress code, and also hopefully get rid of the multiple loops required to iterate over both menus.

If we create an object, an Iterator, that encapsulates the way we iterate through a collection of objects:
```java
// for ArrayList
Iterator iterator = breakfastMenu.createIterator();
while (iterator.hasNext()) { 
  MenuItem menuItem = iterator.next(); 
}

// for Array
Iterator iterator = lunchMenu.createIterator();
while (iterator.hasNext()) { 
  MenuItem menuItem = iterator.next(); 
}
```

The Iterator Pattern relies on an interface called Iterator. 

```java
// the Iterator interface
public interface Iterator { 
  boolean hasNext(); 
  MenuItem next(); 
}

// a concrete Iterator that works for the Diner menu (Array)
public class DinerMenuIterator implements Iterator { 
  MenuItem[] items; 
  int position = 0;

  public DinerMenuIterator(MenuItem[] items) { 
    this.items = items; 
  }

  public MenuItem next() { 
    MenuItem menuItem = items[position]; 
    position = position + 1; 
    return menuItem; 
  }

  public boolean hasNext() { 
    if (position >= items.length || items[position] == null) { 
      return false; 
    } else { 
      return true; 
    }
  }
}

// use the iterator
public class DinerMenu {
  static final int MAX_ITEMS = 6; 
  int numberOfItems = 0; 
  MenuItem[] menuItems;

  // constructor here...
  // addItem here...

  public Iterator createIterator() { 
    return new DinerMenuIterator(menuItems); 
  }
  // other menu methods here
}

public class Waitress {
  PancakeHouseMenu pancakeHouseMenu; 
  DinerMenu dinerMenu;

  public Waitress(PancakeHouseMenu pancakeHouseMenu, DinerMenu dinerMenu) { 
    this.pancakeHouseMenu = pancakeHouseMenu; 
    this.dinerMenu = dinerMenu; 
  }

  public void printMenu() {
    Iterator pancakeIterator = pancakeHouseMenu.createIterator(); 
    Iterator dinerIterator = dinerMenu.createIterator();
    System.out.println("MENU\n----\nBREAKFAST"); 
    printMenu(pancakeIterator); 
    System.out.println("\nLUNCH"); 
    printMenu(dinerIterator);
  }

  private void printMenu(Iterator iterator) {
    while (iterator.hasNext()) { 
      MenuItem menuItem = iterator.next(); 
      System.out.print(menuItem.getName() + ", "); 
      System.out.print(menuItem.getPrice() + " -- "); 
      System.out.println(menuItem.getDescription()); 
    }
  }

  // other methods here
}

```

Next, we can clean up the code using java.util.Iterator:
```java
public Iterator<MenuItem> createIterator() {
  return menuItems.iterator();
}

import java.util.Iterator;
public class DinerMenuIterator implements Iterator<MenuItem> { 
  MenuItem[] items; 
  int position = 0;

  public DinerMenuIterator(MenuItem[] items) { 
    this.items = items; 
  }

  public MenuItem next() { 
    //implementation here 
  }

  public boolean hasNext() { 
    //implementation here 
  }

  public void remove() {
    throw new UnsupportedOperationException (
      "You shouldn't be trying to remove menu items."
    );
  }
}

public interface Menu {
  public Iterator<MenuItem> createIterator();
}

import java.util.Iterator;
public class Waitress {
  Menu pancakeHouseMenu; 
  Menu dinerMenu;

  public Waitress(Menu pancakeHouseMenu, Menu dinerMenu) { 
    this.pancakeHouseMenu = pancakeHouseMenu; 
    this.dinerMenu = dinerMenu; 
  }

  public void printMenu() {
    Iterator<MenuItem> pancakeIterator = pancakeHouseMenu.createIterator(); 
    Iterator<MenuItem> dinerIterator = dinerMenu.createIterator(); 
    System.out.println("MENU\n----\nBREAKFAST"); 
    printMenu(pancakeIterator); 
    System.out.println("\nLUNCH"); 
    printMenu(dinerIterator);
  }

  private void printMenu(Iterator iterator) { 
    while (iterator.hasNext()) { 
      MenuItem menuItem = iterator.next(); 
      System.out.print(menuItem.getName() + ", "); 
      System.out.print(menuItem.getPrice() + " -- "); 
      System.out.println(menuItem.getDescription()); 
    }
  }

  // other methods here
}

```

**The Iterator Pattern** provides a way to access the elements of an aggregate object sequentially, without exposing its underlying representation.

Design Principle: A class should have only one reason to change.

We know we want to avoid change in our classes, because modifying code provides all sorts of opportunities for problems to creep in. Having two ways to change increases the probability the class will change in the future, and when it does, it's going to affect two aspects of your design.









