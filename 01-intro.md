# Intro to Design Patterns
The best way to use patterns is to load your brain with them and then recognize places in your designs and existing applications where you can apply them. Instead of code reuse, with patterns you get experience reuse.

Example: A company makes a duck simulation game. The initial designers of the system used standard OO techniques and created one Duck superclass from which all other duck types inherit. The display() method in the Duck class in abstract, and each duck subtype is responsible for implementing its own display() behavior for how it looks on the screen. Lots of other types of ducks inherit from the Duck class. 

```mermaid
classDiagram
  Duck <|-- MallardDuck
  Duck <|-- RedheadDuck
  Duck <|-- OtherDucksEtc
  class Duck {
    ...
    quack()
    swim()
    display()
  }
  class MallardDuck{
    ...
    display()
  }
  class RedheadDuck{
    ...
    display()
  }
  class OtherDucksEtc{
    ...
    display()
  }
```

Now, the company needs the ducks to fly. 



























