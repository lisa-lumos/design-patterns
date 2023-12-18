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

Restaurant 1 has this ArrayList menu implementations:
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
































