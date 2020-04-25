##### Single Responsibility Principle
https://github.com/jszlenk/Solid-Design-Principles-in-Java/tree/master/src/SPR

This is David's note:
https://craftercoder.xyz/blog/single-responsibility-principle

https://craftercoder.xyz/blog/interface-segregation-principle
```
Single Responsibility Principle
This principle states that each class should have one, and only one reason to change. The responsibility of the class should be scoped to a specific functionality and encapsulated within a single class or module. Primarily it's concerned with limiting the impact of future change.
```
This is a main file for Single Responsibility Principle in code:
```
package SPR;

class Spr {
    public static void main(String[] args) throws Exception {
        Note note = new Note();
        note.addComment("First note");
        note.addComment("Second note");

        Persistence persistence = new Persistence();
        String filename = "notes.txt";
        persistence.saveToFile(note, filename);

        System.out.println(note);
        Runtime.getRuntime().exec("gedit " + filename);
    }
}
```
Here we create a note and also a persistence instance. We then call persistence.saveToFile(note, filename) on parsistence. 

This is the Note class:
```
package SPR;

import java.util.ArrayList;
import java.util.List;

class Note {
    private final List<String> comments = new ArrayList<>();
    private static int count = 0;

    void addComment(String text) {
        comments.add("" + (++count) + ": " + text);
    }

    @Override
    public String toString() {
        return String.join(System.lineSeparator(), comments);
    }
}s
```
It has addComment and a toString method. Nothing else. Also the Persistence() class only does one thing:
```
package SPR;

import java.io.PrintStream;

class Persistence {
    void saveToFile(Note note, String filename) throws Exception {
        try (PrintStream out = new PrintStream(filename)) {
            out.println(note.toString());
        }
    }
}
```
The job here is to saveToFile. Each class has a single responsibility.


##### Open Closed Principle:
```
Meyer states that:

"Software entities (classes, modules, functions, etc.) should be open for extension, but closed for modification"

And Uncle Bob later expressed this as:

You should be able to extend the behavior of a system without having to modify that system.

Let's suppose you have written code in one place within your application and that code is now used elsewhere. You want to be able to adapt this to different purposes by writing additional code and not by modifying the code itself. This is because modifying it will likely cause changes to be made everywhere it is used.
```

This is an example of Open Closed:
https://github.com/jszlenk/Solid-Design-Principles-in-Java/blob/master/src/OCP/Ocp.java
```
package OCP;

import java.util.List;

public class Ocp {
    public static void main(String[] args) {
        Product apple = new Product("Apple", Color.RED, Size.SMALL);
        Product tree = new Product("Tree", Color.GREEN, Size.MEDIUM);
        Product house = new Product("House", Color.BLUE, Size.LARGE);

        List<Product> products = List.of(apple, tree, house);
        ImplFilter implFilter = new ImplFilter();

        implFilter.filter(products, new ColorSpecification(Color.GREEN))
                .forEach(p -> System.out.println(p.name + " is green"));

        implFilter.filter(products, new SizeSpecification(Size.LARGE))
                .forEach(p -> System.out.println(p.name + " is large"));

        implFilter.filter(products,
                new AndSpecification<>(new ColorSpecification(Color.BLUE), new SizeSpecification(Size.LARGE)))
                .forEach(p -> System.out.println(p.name + " is large and blue"));
    }
}
```
Here we are able to change the behaviour of each product according to name color and size. We can then use different versions of our ColorSpecification, SizeSpecification to show the correct products. This is the POJO of the Product:
```
package OCP;

class Product {
    String name;
    Color color;
    Size size;

    Product(String name, Color color, Size size) {
        this.name = name;
        this.color = color;
        this.size = size;
    }
}
```
Size and Color are enums:
```
  
package OCP;

public enum Size {
    SMALL,
    MEDIUM,
    LARGE,
}
```
Color:
```
package OCP;

public enum Color {
    RED,
    GREEN,
    BLUE
}
```
We then have a filter interface which returns a Stream():
```
package OCP;

import java.util.List;
import java.util.stream.Stream;

public interface Filter<T> {
    Stream<T> filter(List<T> items, Specification<T> spec);
}
```
The implementation of the Filter interface is the following:
```
package OCP;

import java.util.List;
import java.util.stream.Stream;

public class ImplFilter implements Filter<Product> {
    public Stream<Product> filter(List<Product> items, Specification<Product> spec) {
        return items.stream().filter(spec::isSatisfied);
    }
}
```
This then allows us to use different specifications for each type of product:
```
  
package OCP;

public interface Specification<T> {
    boolean isSatisfied(T item);
}
```
Each class is then able to override the isSatisfied() method:
```
package OCP;

public class AndSpecification<T> implements Specification<T> {
    private Specification<T> first, second;

    AndSpecification(Specification<T> first, Specification<T> second) {
        this.first = first;
        this.second = second;
    }

    @Override
    public boolean isSatisfied(T item) {
        return first.isSatisfied(item) && second.isSatisfied(item);
    }
}
```
Here the and specification enables us to combine the Specifications:
```
package OCP;

public class ColorSpecification implements Specification<Product>{
    private Color color;

    ColorSpecification(Color color) {
        this.color = color;
    }

    public boolean isSatisfied(Product p) {
        return p.color == color;
    }
}
```
The is the SizeSpecification:
```
package OCP;

public class SizeSpecification  implements Specification<Product>{
    private Size size;

    SizeSpecification(Size size) {
        this.size = size;
    }

    public boolean isSatisfied(Product p) {
        return p.size == size;
    }
}
```
Finally we implement the system in the following manner:
```
package OCP;

import java.util.List;

public class Ocp {
    public static void main(String[] args) {
        Product apple = new Product("Apple", Color.RED, Size.SMALL);
        Product tree = new Product("Tree", Color.GREEN, Size.MEDIUM);
        Product house = new Product("House", Color.BLUE, Size.LARGE);

        List<Product> products = List.of(apple, tree, house);
        ImplFilter implFilter = new ImplFilter();

        implFilter.filter(products, new ColorSpecification(Color.GREEN))
                .forEach(p -> System.out.println(p.name + " is green"));

        implFilter.filter(products, new SizeSpecification(Size.LARGE))
                .forEach(p -> System.out.println(p.name + " is large"));

        implFilter.filter(products,
                new AndSpecification<>(new ColorSpecification(Color.BLUE), new SizeSpecification(Size.LARGE)))
                .forEach(p -> System.out.println(p.name + " is large and blue"));
    }
}
```

##### Liskov substitution:
```
First introduced by Barbara Liskov, in a 1987 conference keynote address titled Data abstraction and hierarchy, the Liskov substitution principle (LSP) is a particular definition of a subtyping relation, called (strong) behavioral subtyping.

It can be stated as such:

If S is a subtype of T, then objects of type T in a program may be replaced with objects of type S without altering any of the desirable properties of that program

Or in duck terms:

If it looks like a 🦆, quacks like a 🦆 but needs batteries - you probably have the wrong level of abstraction

This dictates that, for your code to adhere to LSP, all subclasses* must be completely interchangeable with their parent class.
```
Here we have an illustration of Liskov substitution using a Rectangle Factory:
```
package LSP;

public class Lsp {
    public static void main(String[] args) {
        Rectangle rectangle = RectangleFactory.newRectangle(2, 4);
        Rectangle square = RectangleFactory.newSquare(4);

        showArea(rectangle);
        showArea(square);
    }

    private static void showArea(Rectangle figure) {
        System.out.println("Expected area is " + figure.getArea() + " for " + figure.toString());
    }
}
```
Here the RectangleFactory returns a square or rectangle depending on what we require in the client:
```
package LSP;

public class RectangleFactory {
    public static Rectangle newSquare(int side) {
        return new Rectangle(side, side);
    }

    public static Rectangle newRectangle(int width, int height) {
        return new Rectangle(width, height);
    }
}
```
This is the Rectangle itself:
```
package LSP;

public class Rectangle {
    private int width, height;

    Rectangle(int width, int height) {
        this.width = width;
        this.height = height;
    }

    int getArea() {
        return width * height;
    }

    @Override
    public String toString() {
        return "Figure(" +
                "width=" + width +
                ", height=" + height +
                ')';
    }
}
```
We can then create a Rectangle or a Square depending on what we send to the Factory and each instance is interchangeable:
```
package LSP;

public class Lsp {
    public static void main(String[] args) {
        Rectangle rectangle = RectangleFactory.newRectangle(2, 4);
        Rectangle square = RectangleFactory.newSquare(4);

        showArea(rectangle);
        showArea(square);
    }

    private static void showArea(Rectangle figure) {
        System.out.println("Expected area is " + figure.getArea() + " for " + figure.toString());
    }
}
```
through our showArea method.


###### Interface Segregation
```
Robert C. Martin (Uncle Bob) defined the interface-segregation principle (ISP) whilst working with Xerox to improve the software for their new printers. He stated it as:

A client should never be forced to implement an interface that it doesn't use. Nor should clients be forced to depend on methods they do not use.

The motivation behind this principle is to move towards a codebase that relies on thin abstract interfaces. It makes it easier for clients to have less dependent factors between modules them, thus reducing coupling, increasing cohesion and improving the maintainabilty of the code.

This is in a similar vain to the Single Responsibility Principle. Your abstractions should be small, individual units of business logic so that your clients implement the slice of the pie they need not the whole pie (nor the ice cream, cherry or sprinkles).
```

This is an illustration of the Interface Segregation Principle using a printer:
```
package ISP;

public class Isp {
    public static void main(String[] args) throws Exception {

        Document document = new Document();
        document.addDocument("Offer");
        document.addDocument("Invoice");

        PrinterHP printerHP = new PrinterHP();
        printerHP.Print(document);

        ScannerSony scannerSony = new ScannerSony();
        scannerSony.Scan(document);

        PhotocopierHP photocopierHP = new PhotocopierHP();
        photocopierHP.Print(document);

        FaxPanasonic faxPanasonic = new FaxPanasonic();

        MultiFunctionMachine multiFunctionMachine = new MultiFunctionMachine(printerHP, scannerSony, faxPanasonic);
        multiFunctionMachine.InternetFax(document);
    }
}
```
Here we have a simple document class:
```
package ISP;

import java.util.ArrayList;
import java.util.List;

public class Document {
    private final List<String> documents = new ArrayList<>();

    void addDocument(String text) {
        documents.add(text);
    }

    @Override
    public String toString() {
        return String.join(", ", documents);
    }
}
```
We have a Multifunction device interface:
```
package ISP;

public interface MultiFunctionDevice extends Printer, Scanner, Fax {
}
```
and a Multifunction Machine that implements the device interface:
```
package ISP;

public class MultiFunctionMachine implements MultiFunctionDevice {
    private Printer printer;
    private Scanner scanner;
    private Fax fax;

    MultiFunctionMachine(Printer printer, Scanner scanner, Fax fax) {
        this.printer = printer;
        this.scanner = scanner;
        this.fax = fax;
    }

    public void Print(Document d) throws Exception {
        printer.Print(d);
    }

    public void Scan(Document d) throws Exception {
        scanner.Scan(d);
    }

    public void InternetFax(Document d) throws Exception {
        fax.InternetFax(d);
    }
}
```
We then have different interfaces which can interact as this device:
```
package ISP;

public interface Printer {
    void Print(Document d) throws Exception;
}
```
and:
```
package ISP;

public class PrinterHP implements Printer {
    public void Print(Document d) {
        System.out.println("Print the text from the document: " + d);
    }
}
```
Scanner:
```
  
package ISP;

public interface Scanner {
    void Scan(Document d) throws Exception;
}
```
and:
```
  
package ISP;

public class ScannerSony implements Scanner {
    public void Scan(Document d) {
        System.out.println("Scan the text from the document: " + d);
    }
}
```
and photocopier:
```
package ISP;

class PhotocopierHP implements Printer, Scanner {
    public void Print(Document d) {
        System.out.println("Print the text from the document: " + d);
    }

    public void Scan(Document d) {
        System.out.println("Scan the text of the document: " + d);
    }
}
```
and fax:
```
package ISP;

public interface Fax {
    void InternetFax(Document d) throws Exception;
}
```
and here:
```
package ISP;

public class FaxPanasonic implements Fax {

    public void InternetFax(Document d) {
        System.out.println("Fax the text from the document: " + d);
    }
}
```
We then look again at the ISP class:
```
package ISP;

public class Isp {
    public static void main(String[] args) throws Exception {

        Document document = new Document();
        document.addDocument("Offer");
        document.addDocument("Invoice");

        PrinterHP printerHP = new PrinterHP();
        printerHP.Print(document);

        ScannerSony scannerSony = new ScannerSony();
        scannerSony.Scan(document);

        PhotocopierHP photocopierHP = new PhotocopierHP();
        photocopierHP.Print(document);

        FaxPanasonic faxPanasonic = new FaxPanasonic();

        MultiFunctionMachine multiFunctionMachine = new MultiFunctionMachine(printerHP, scannerSony, faxPanasonic);
        multiFunctionMachine.InternetFax(document);
    }
}
```
We can now see that we have a document and all the different devices. We can then use Print on each class or alternatively use the MultiFunctionMachine to call InternetFax to feed in all the classes and then print the document in the manner in which we wish:


##### Dependency Inversion:
```
The Dependency Inversion Principle (DIP), as it is known, is primarily concerned with decoupling software modules. It states that:

Entities must depend on abstractions not on concretions. Concretions should depend on abstractions. High level modules must not depend on the low level modules, they should instead both depend on abstractions (e.g. interfaces).

As it dictates that both high-level and low-level objects must depend on the same abstraction, this design principle inverts the way some people may think about object-oriented programming, hence the name.

It sounds a lot more complex than it often is. Adhering to DIP is often a by-product of applying the Open/Closed Principle and the Liskov Substitution Principle to your code. This would generally mean you have interfaces that are implemented by easily interchangeable concretions that are closed to modification and open to extension.

I really can't say this better than Uncle Bob himself so I'll use his words to drive home the point:

Consider the implications of high level modules that depend upon low level modules. It is the high level modules that contain the important policy decisions and business models of an application. It is these models that contain the identity of the application. Yet, when these modules depend upon the lower level modules, then changes to the lower level modules can have direct effects upon them; and can force them to change.

This predicament is absurd! It is the high level modules that ought to be forcing the low level modules to change. It is the high level modules that should take precedence over the lower level modules. High level modules simply should not depend upon low level modules in any way
```
Here we have the DIP class to illustrate the point:
```
package DIP;

class Dip {
    public static void main(String[] args) {
        Person parent = new Person("Jakob", Role.PARENT);
        Person child1 = new Person("Alex", Role.CHILD);
        Person child2 = new Person("Anna", Role.CHILD);

        Relationships relationships = new Relationships();
        relationships.addParentAndChild(parent);
        relationships.addParentAndChild(child1);
        relationships.addParentAndChild(child2);

        Research research = new Research(parent, relationships);
        System.out.println(research);

    }
}
```
We are able to create Parent or child with our new Person class and then instantiate a new Research class with parent and relationships. The Person is a POJO:
```
package DIP;

public class Person {
    public String name;
    public Enum role;

    public Person(String name, Enum role) {
        this.name = name;
        this.role = role;
    }
}
```
We also have a role enum:
```
package DIP;

public enum Role {
    PARENT,
    CHILD
}
```
Next we have a RelationshipBrowser interface:
```
package DIP;

import java.util.List;

public interface RelationshipBrowser {
    List<Person> findAllChildrenOf(Person person);
}
```
This is implemented through the Relationship class:
```
package DIP;

import java.util.ArrayList;
import java.util.List;
import java.util.stream.Collectors;

class Relationships implements RelationshipBrowser {
    public List<Person> findAllChildrenOf(Person person) {

        return relations.stream()
                .filter(p -> p.role.equals(Role.CHILD))
                .collect(Collectors.toList());
    }

    private List<Person> relations = new ArrayList<>();

    void addParentAndChild(Person person) {
        relations.add(person);
    }
}
```
We can then use the Research class to print out our information on the relationships:
```
package DIP;

import java.util.List;

public class Research {

    Research(Person parent, RelationshipBrowser browser) {
        List<Person> children = browser.findAllChildrenOf(parent);
        for (Person child : children)
            System.out.println(parent.name + " has a child called " + child.name);
    }

    @Override
    public String toString() {
        return "Research end";
    }
}
```
We can then revisit the DIP to look again at how it is instantiated:
```
package DIP;
s
class Dip {
    public static void main(String[] args) {
        Person parent = new Person("Jakob", Role.PARENT);
        Person child1 = new Person("Alex", Role.CHILD);
        Person child2 = new Person("Anna", Role.CHILD);

        Relationships relationships = new Relationships();
        relationships.addParentAndChild(parent);
        relationships.addParentAndChild(child1);
        relationships.addParentAndChild(child2);

        Research research = new Research(parent, relationships);
        System.out.println(research);

    }
}
```