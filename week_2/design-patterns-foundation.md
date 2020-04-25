
###### I have now got to Section 3 - Adapter Pattern - for Programmming Foundations: Design Patterns
Eric Freeman and Elisabeth Robson
https://www.linkedin.com/learning/programming-foundations-design-patterns-2/using-the-adapter-pattern


This is an example of the Adapter pattern:

![Screenshot 2020-03-13 at 08 00 39](https://user-images.githubusercontent.com/27693622/76601560-13378600-6501-11ea-9017-82569afc97bd.png)

Here we are using composition to get flexibility in our design. The client is composed with the class with the target interface.

The adapter is composed with the adaptee
- The adapter delegates calls to the adaptee and returns any needed value

This is the solution to the adapter challenge:
![Screenshot 2020-03-13 at 08 03 33](https://user-images.githubusercontent.com/27693622/76601592-264a5600-6501-11ea-9ff0-64f94c410872.png)

I am now on the Observer Pattern - I will continue with this tonight - 
https://www.linkedin.com/learning/programming-foundations-design-patterns-2/understanding-the-observer-pattern


I am now on the Observer Pattern:
![Screenshot 2020-03-13 at 12 00 24](https://user-images.githubusercontent.com/27693622/76619424-bd73d580-6522-11ea-9fd5-9b8092eff3d1.png)

This includes a subject which registers an observer and notifies them with with notifyObserver() which calls update() on the ConcreteObserver.

These are the advantages of loose coupling:
- Subjects and observers are loosely coupled
- They interact but have little knowledge of each other
- Subject knows only that the observer implements a specific interface
- Subject doesn't need to know the concrete class of the observers
- Subject relies on a list of observers
- observers can be added, removed or replaced at any time
- subject doesn't care; it keeps doing its job

The Subject Interface would look like this in code:
```
public interface Subject {
 public void registerObserver(Observer o);
 public void removeObserver(Observer o);
 public void notifyObservers();
}
```
The Concrete subject looks like this:
```
public class SimpleSubject implements Subject {
 private ArrayList<Observer> observers;
 private int value = 0;
 public SimpleSubject() {
  observers = new ArrayList<Observer>();
 }
 public void registerObserver(Observer o){
  //... add observer to the list
 }

 public void removeObserver(Observer o) {
  //... remove observer the list
 }

 public void notifyObservers() {
  for (Observer observer : observers) {
   observer.update(value);
  }
 }

 public void setValue(int value) {
  this.value = value;
  notifyObservers();
 }
}
```
This is the Observer Interface:
```
public interface Observer {
 public void update(int value);
}
```
This is an example of the concrete Observer:
```
public class SimpleObserver implements Observer {
 private int value;
 private Subject simpleSubject;

 public SimpleObserver(Subject simpleSubject) {
  this.simpleSubject = simpleSubject;
  simpleSubject.registerObserver(this);
 }

 public void update(int value){
  this.value = value;
  display();
 }

 public void display() {
  System.out.println("Value: " + value);
 }
}
```
Observer pattern loosely couple objects together whenever objects notified when object updated.

![Screenshot 2020-03-13 at 12 17 48](https://user-images.githubusercontent.com/27693622/76620303-cb2a5a80-6524-11ea-8702-324b9b020b19.png)

This is the solution for the Observer pattern:

![Screenshot 2020-03-13 at 12 19 29](https://user-images.githubusercontent.com/27693622/76620361-e7c69280-6524-11ea-97f1-bf0ce1e66f09.png)

This has a new subject interface implemented by the weather station.

List of observers to the weather station. We use the update() method on each class with notifyObserver().

##### Section 5: Decorator Pattern

This is inheritance for the Coffee beverage question:

![Screenshot 2020-03-13 at 12 23 28](https://user-images.githubusercontent.com/27693622/76620623-7a673180-6525-11ea-944b-6b5eb1714b1e.png)

This would lead to alot of classes for all the possible drinks. We can't sort out the increase in cost.

![Screenshot 2020-03-13 at 12 25 16](https://user-images.githubusercontent.com/27693622/76620719-ba2e1900-6525-11ea-901b-ea1fd84b04bd.png)

Then we might do the following:
```
HouseBlend drink = new HouseBlend();
drink.setSoy();
drink.setWhip();
float cost = drink.cost();
```
We would then need to have a series of if statements:
```
if (hasMilk()) {
 cost += .10;
}

if (hasSoy()) {
 cost += .20;
}

if (hasWhip()) {
 cost += .10
}

if (hasMocha()) {
 cost += .15;
}
```

##### I have now got to Section 5 on the Decorator Pattern. 
- The other topics I will look at include:
5. The Decorator Pattern
6. The Iterator Pattern
7. The Factory Patterns

#### Section 5 - Decorator Pattern
##### Open Closed Principle

Code should be open for extension but closed for modification.

How do we design Objects to fulfil this principle.

The Decorator Pattern can help with this principle

Composition allows us to add behaviour without altering existing code

![Screenshot 2020-03-14 at 13 29 36](https://user-images.githubusercontent.com/27693622/76682918-ea45ec80-65f7-11ea-92d1-fea3a25b21a0.png)



##### This could be an example of the decorator pattern for calculating tax:
https://dev4devs.com/2016/06/09/java-design-pattern-how-to-use-with-code-examples-and-project-for-you-understand/

Example:
```
public class DecoratorTest {

    @Test
    public void testDecorator() {
        Tax taxComplexy = new CityTax(new FederalTax());
        Invoice invoice = new Invoice(1000.0);
        double result = taxComplexy.calculate(invoice);
        TestCase.assertEquals(result, 150);
    }
}
```
Above we have a TestDecorator. We also have a Tax abstract class:
```
public abstract class Tax {

    private final Tax otherTax;

    public Tax(Tax otherTax) {
        this.otherTax = otherTax;
    }

    // construtor default
    public Tax() {
        this.otherTax = null;
    }

    protected double calculateOtherTax(Invoice invoice) {
        // If don't exist exist
        if(otherTax == null) return 0;
        return otherTax.calculate(invoice);
    }

    public abstract double calculate(Invoice invoice);

}
```
The FederalTax inherits from the abstract Tax class:
```
public class FederalTax extends Tax {

    public FederalTax(Tax otherTax) {
        super(otherTax);
    }

    public FederalTax(){}

    @Override
    public double calculate(Invoice invoice) {
        return invoice.getValue() * 0.05 + this.calculateOtherTax(invoice);
    }

}
public class CityTax extends Tax {

    public CityTax(Tax otherTax) {
        super(otherTax);
    }

    public CityTax(){}

    @Override
    public double calculate(Invoice invoice) {
        return invoice.getValue() * 0.1 + this.calculateOtherTax(invoice);
    }
}
```
Finall we see the invoice for calculating money owed:
```
public class Invoice {

    private double value;

    public Invoice(double value) {
        this.value = value;
    }

    public double getValue() {
        return value;
    }

    private void setValue(double value) {
        this.value = value;
    }
}
```


![Screenshot 2020-03-14 at 13 38 10](https://user-images.githubusercontent.com/27693622/76683084-1746cf00-65f9-11ea-9edf-8b040e853ab0.png)

The Decorator Pattern attaches additional responsibilities to an object dynamically. Decorators provide a flexible alternative to subclassing for extending functionality.

![Screenshot 2020-03-14 at 13 41 45](https://user-images.githubusercontent.com/27693622/76683136-92a88080-65f9-11ea-9fb7-2ae25534e426.png)

This is what the design looks like for the Coffee Shop:
![Screenshot 2020-03-14 at 13 49 10](https://user-images.githubusercontent.com/27693622/76683250-9dafe080-65fa-11ea-9aba-c05c33e8d81c.png)

The Beverage class is the component superclass:
![Screenshot 2020-03-14 at 13 50 13](https://user-images.githubusercontent.com/27693622/76683260-bfa96300-65fa-11ea-94f0-8e04ef735c45.png)

It returns the description which is overwritten by each component.

![Screenshot 2020-03-14 at 13 51 40](https://user-images.githubusercontent.com/27693622/76683288-f2ebf200-65fa-11ea-8a51-431569a88302.png)


![Screenshot 2020-03-14 at 13 52 36](https://user-images.githubusercontent.com/27693622/76683301-17e06500-65fb-11ea-90a0-5fd5ef90f475.png)


![Screenshot 2020-03-14 at 13 53 17](https://user-images.githubusercontent.com/27693622/76683327-2f1f5280-65fb-11ea-9d75-43ead836be02.png)

This is an example order from Main:
![Screenshot 2020-03-14 at 13 55 15](https://user-images.githubusercontent.com/27693622/76683361-73aaee00-65fb-11ea-85ad-e877749961a0.png)

##### The challenge set was to design a system for the pizza store using the decorator pattern.

This was the solution:
![Screenshot 2020-03-14 at 14 47 03](https://user-images.githubusercontent.com/27693622/76684306-b9b78000-6602-11ea-8641-37f19dd968fa.png)

##### 6. The iterator Pattern

There are various ways of storing objects eg:
```
String[] menuItems = new String[MAX_ITEMS];
menuItems[0] = "Regular Pancake Breakfast";
menuItems[1] = "Blueberry Pancakes";
```
Another way would be a list:
```
ArrayList<String> menuItems = new ArrayList<String>();
menuItems.add("Regular Pancake Breakfast");
menuItems.add("Blueberry Pancakes");
```
What if we have to write code to operate over several items.

We might iterate over items in a menu like this:
```
public void print(items) {
 for (int i; i < items.length; i++) {
  String item = items[i];
  // print the item
 }
}
```
If we change to an arrayList we would have to rewrite all the iteration code to items.size...
How do we encapsulate iteration?

##### The Iterator Pattern
The Iterator Pattern provides a way to access the elements of an aggregate object sequentially without exposing its underlying representation.

What is an aggregate object?
- Arrays
- Java Collection classes like ArrayList
- Lists
- Sets
- Maps
- Dictionaries

We need to be able to access elements without knowing how it is implemented.

The Iterator Object but the client doesn't have to know the details.
To iterate over an Aggregate Object:
1. Ask object for its iterator
2. Use the iterator to iterate through the items in the aggregate
3. Iteration code now works with any kind of aggregate object.

###### This is a class diagram for the iterator pattern:
![Screenshot 2020-03-14 at 15 13 31](https://user-images.githubusercontent.com/27693622/76684744-6b0be500-6606-11ea-85b0-471505120762.png)

This is how it looks like for the cafe menu:

![Screenshot 2020-03-14 at 15 16 31](https://user-images.githubusercontent.com/27693622/76684788-cf2ea900-6606-11ea-9e0b-afb801277ccd.png)

At the moment the cafe uses two different iteration for loops for the PancakeHouseMenu and DinerMenu respectively:

![Screenshot 2020-03-14 at 15 17 40](https://user-images.githubusercontent.com/27693622/76684814-f6857600-6606-11ea-9eaf-f821e3797528.png)

This is the DinerMenuIterator:

![Screenshot 2020-03-14 at 15 19 03](https://user-images.githubusercontent.com/27693622/76684848-292f6e80-6607-11ea-81a4-ffe5f2ab5169.png)


![Screenshot 2020-03-14 at 15 18 51](https://user-images.githubusercontent.com/27693622/76684877-554aef80-6607-11ea-831c-13e229153a4e.png)

We can now use one method to iterate over either menu:


![Screenshot 2020-03-14 at 15 21 03](https://user-images.githubusercontent.com/27693622/76684899-77447200-6607-11ea-829e-85f50ad39972.png)

Now the cafe is more flexible and can work with any menu as long as the menu has an Iterator to work with it.

Java.utils.iterator gives us the interator() method which can be used with any type of collection.

##### Responsibility
Every responsibility gives the class a reason to change.
A class should have only one reason to change.

We separate the iteration to another class in order to avoid multiple responsibility such as creating a list and then iterating over it.

Java's enhanced for statement is designed for iteration:
```
for (Animal a : animals) {
 a.describe();
 a.makeSound();
}
```
Python for in and Javascript for of use the Iterator pattern.

All of these move the implementation away from the code.

This is an example of an iterator in ruby:
https://refactoring.guru/design-patterns/iterator/ruby/example
```
class AlphabeticalOrderIterator
  # In Ruby, the Enumerable mixin provides classes with several traversal and
  # searching methods, and with the ability to sort. The class must provide a
  # method each, which yields successive members of the collection.
  include Enumerable

  # This attribute indicates the traversal direction.
  attr_accessor :reverse
  private :reverse

  # @return [Array]
  attr_accessor :collection
  private :collection

  # @param [Array] collection
  # @param [Boolean] reverse
  def initialize(collection, reverse = false)
    @collection = collection
    @reverse = reverse
  end

  def each(&block)
    return @collection.reverse.each(&block) if reverse

    @collection.each(&block)
  end
end

class WordsCollection
  # @return [Array]
  attr_accessor :collection
  private :collection

  def initialize(collection = [])
    @collection = collection
  end

  # The `iterator` method returns the iterator object itself, by default we
  # return the iterator in ascending order.
  def iterator
    AlphabeticalOrderIterator.new(@collection)
  end

  # @return [AlphabeticalOrderIterator]
  def reverse_iterator
    AlphabeticalOrderIterator.new(@collection, true)
  end

  # @param [String] item
  def add_item(item)
    @collection << item
  end
end

# The client code may or may not know about the Concrete Iterator or Collection
# classes, depending on the level of indirection you want to keep in your
# program.
collection = WordsCollection.new
collection.add_item('First')
collection.add_item('Second')
collection.add_item('Third')

puts 'Straight traversal:'
collection.iterator.each { |item| puts item }
puts "\n"

puts 'Reverse traversal:'
collection.reverse_iterator.each { |item| puts item }
```
Here is the result:
```
Straight traversal:
First
Second
Third

Reverse traversal:
Third
Second
First
```

##### Factory Pattern:
```
Duck duck;

if (picnic) {
 duck = new MallardDuck();
} else if (hunting) {
 duck = new DecoyDuck();
} else if (inBathTub) {
 duck = new RubberDuck();
}
```
Here we are making run time decisions on what duck to instantiate. 

Here is a Pizza example:

```
Pizza orderPizza(String type) {
 Pizza pizza;

 if (type.equals("cheese")) {
  pizza = new CheesePizza();
 } else if (type.equals("greek)) {
  pizza = new GreekPizza();
 } else if (type.equals("pepperoni")) {
  pizza = new PepperoniPizza();
 }
 pizza.prepare();
 pizza.cook();
 return pizza;
}
```
The methods creates different type of pizza depending on type. We would like to close this code for modification.

How can we encapsulate the pizza creation code?

We will create a PizzaFactory:
```
public class SimplePizzaFactory {
 public Pizza createPizza(String type) {
  Pizza pizza = null;

  if (type.equals("cheese")) {
   pizza = new CheesePizza();
  } else if (type.equals("greek")) {
   pizza = new GreekPizza();
  } else if (type.equals("pepperoni")) {
   pizza = new PepperoniPizza();
  } else if (type.equals("veggie")) {
   pizza = new VeggiePizza();
  }
  return pizza;
 }
}
```
Now we can do:
```
Pizza orderPizza(String type) {
 Pizza pizza = factory.createPizza(type);

 pizza.prepare();
 pizza.cook();
 return pizza;
}
```
The orderPizza method does not have to worry about the type of the pizza anymore. It implements the pizza interface. This is the Simple Factory Pattern and it is a very common way of dealing with object creation:
![Screenshot 2020-03-14 at 15 40 28](https://user-images.githubusercontent.com/27693622/76685253-271adf00-660a-11ea-99a3-ebce20b3fd1b.png)

This is the first step for understanding the more powerful Factory Patterns.

What happens if we have multiple Stores. We may need 2 more factories to create different kind of pizza. 

We can use the Factory Method Pattern.

##### Factory Method Pattern

The Factory Method pattern defines an interface for creating an object, but lets subclasses decide which class to instantiate. Factory Method lets a class defer instantiation to subclasses.

![Screenshot 2020-03-14 at 15 59 03](https://user-images.githubusercontent.com/27693622/76685584-c9d45d00-660c-11ea-9001-216c60226e36.png)

This is the same as the SimpleFactoryMethod but uses Chicago StylePizzaStore and newYorkPizzaStore to create pizzas.

##### Creating a Pizza with Factory Method:
```
PizzaStore store = new NYPizzaStore();
store.orderPizza("cheese");
```
In orderPizza we have:
```
Pizza pizza = createPizza("cheese");
pizza.prepare();
pizza.bake();
pizza.cut();
pizza.box();
```
Once the createPizza has returned the correct pizza the method in the store in question goes about creating the pizza.

This is the code for the NYPizzaStore:
```
public class NYPizzaStore extends PizzaStore {
 Pizza createPizza(String item) {
  if (item.equals("cheese)) {
   return new NYStyleCheesePizza();
  } else if (item.equals("veggie")) {
   return new NYStyleVeggiePizza();
  } else if (item.equals("clam")) {
   return new NYStyleClamPizza();
  } else if (item.equals("pepperoni")) {
   return new NYStylePepperoniPizza();
  } else return null;
 }
}
}
```
Likewise there is a ChicagoStylePizzaStore class. These are implemented by the orderPizza method from PizzaStore:
```
public abstract class PizzaStore {
 abstract Pizza createPizza(String item);

 public Pizza orderPizza(String type) {
  Pizza pizza = createPizza(type);
  System.out.println("Making a " + pizza.getName());
  pizza.prepare();
  pizza.bake();
  pizza.cut();
  pizza.box();
  return pizza;
 }
}
```
If we create a NYPizzaStore we will implement pizzas according to the store specifications.

##### Challenge
Write code for Zone, its subclasses, and the ZoneFactory class that will create the correct zone based on the zone id that you pass to its createZone() method.

This is the solution:
![Screenshot 2020-03-14 at 16 14 28](https://user-images.githubusercontent.com/27693622/76685869-e6719480-660e-11ea-801c-bd1081e38450.png)

Patterns are not a magic bulet but are not the solution to every problem.

Solve problems in the simplest way possible.

