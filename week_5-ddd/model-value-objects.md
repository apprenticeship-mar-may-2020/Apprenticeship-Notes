##### This is Model Value Objects:
https://github.com/codurance/apprenticeship/wiki/Model,-Value-Objects,-Services
# Model

## Domain

First, to understand what is our model, we need to define what is a Domain. The Domain is the business that we are working on. 
For example, imagine that a customer came to us trying to validate his idea, and for that he wanted a registration page to see how many people would register for his new idea.

Talking to our business expert in login pages we found that:
* We need the `username`,`password`, and the `password_confirmation` fields and those fields should be called `RegistrationData`
* The `password` and `password_confirmation` fields must be equals. 
* The `password` must be stored in a secure manner, using something like BCrypt.
* By the end we will have a `User` with the `username`, and the `password` that we encrypted earlier.

So now we know our domain, we can start with the model.  

## Model

The Model is the representation of a Domain. 
We can create a Model by using things like UML or Event Storming, and its going to be something that technical and non-technical stakeholders can understand. 
It's really important to find the right language for the Model (Ubiquitous Language).

We can document the requirements that we gathered before, we can also create diagrams like this one:
![](https://i.imgur.com/TtJ3I52.png)!

All this is going to be part of our Model. 

# Value Objects

## Brief explanation
Value Objects are the smallest structure that you can have in DDD.

A Value Object has three distinct characteristics:
* it's immutable.
* don't have an identifier.
* is equal to other objects with the same value.

Immutability:
That when you create a value object, you can't change any of the fields in the object, if you need a value object with a different field you create a new one. Values Objects are disposable and you should not worry about creating new instances. 

Don't have a unique identifier:
This means that the value object doesn't have a unique identifier(like an id tied to a primary key). What gives the identity of a Value Object is its own state. 

Is equal to other value objects with the same value: 
If you compare two objects with the same internal state they should be equal, even if they are different instances. 

## Values Objects in Java

One example that we can have for Value Objects, is an API that can register users, to register a new user you need a `username`, a `password`, and the `password_confirmation`.
How should this information be represented as a value object? 

```java
public class RegistrationData {

    public final String username;
    public final String password;
    public final String password_confimation;
 
    public Position(String username, String password, String password_confimation) {
        this.username = username
        this.password = password
        this.password_confimation = password_confimation
    }
}
```

This would cover 2 of the 3 characteristics that we saw: 
1. It's immutable
2. Don't have an identifier

but if we compare both `RegistrationData` objects, they will be different.

```java
var regData1 = new RegistrationData("user", "pwd", "pwd");
var regData2 = new RegistrationData("user", "pwd", "pwd");
System.out.println(regData1.equals(regData2)); // => false
```

Now we need to implement the third characteristic, by making them being compared by their own state.

```java
public class Position {
    ...
    @Override
    public boolean equals(RegistrationData otherRegistrationData) {
        return  this.username.equals(otherRegistrationData.username) &&
                this.password.equals(otherRegistrationData.password) &&
                this.password_confimation.equals(otherRegistrationData.password_confimation)
    }
}
```

After that, we can see that both Value Objects are the same now.

```java
var regData1 = new RegistrationData("user", "pwd", "pwd");
var regData2 = new RegistrationData("user", "pwd", "pwd");
System.out.println(regData1.equals(regData2)); // => true
```

# Services

> A good SERVICE has three characteristics.
>
> 1. The operation relates to a domain concept that is not a natural part of an ENTITY or VALUE OBJECT.
> 2. The interface is defined in terms of other elements of the domain model.
> 3. The operation is stateless.
>
> <cite>Eric Evans</cite>

There are three types of services:
* Application Services
* Domain Services
* Infrastructure Services

## Application Services

> The Application Services are the direct clients of the domain model...
>
> These are responsible for task coordination of use case flows, one service method per flow...
>
> It is a mistake to consider Application Services to be the same as Domain Services. They are not.
>
> We should strive to push all business domain logic into the domain model, whether that be in Aggregates, Value Objects, or Domain Services. Keep Application Services thin, using them only to coordinate tasks on the model.
>
> <cite>Vaughn Vernon</cite>

Note: _application services are equivalent to actions in [Interaction-Driven Design](https://codurance.com/2017/12/08/introducing-idd/)_

We are using this Application Service to coordinate the `purchaseRepository` and the model (in this case, the domain service `PurchasesReportGenerator`):

```java
public class GeneratePurchasesReport {
    ...
    public void execute(Date from, Date to) {
	
        List<Purchase> purchases = purchasesRepository.find(from, to);
        
        PurchasesReport purchasesReport = new PurchasesReportGenerator().generate(purchases);
        
        ...
    } 
}
```

## Domain Services
A Domain Service is a building block where the purpose is to host any code that makes some transformation in the domain, and don't belong to an Entity or a Value Object. Domain Services should be stateless with you passing all the data for the transformation in the parameters and returning the result of the transformation (following the idea of [pure fabrication](https://en.wikipedia.org/wiki/GRASP_(object-oriented_design)#Pure_fabrication)).

> When a significant process or transformation in the domain is not a natural responsibility of an ENTITY or VALUE OBJECT, add an operation to the model as a standalone interface declared as a SERVICE. Define the interface in terms of the language of the model and make sure the operation name is part of the UBIQUITOUS LANGUAGE. Make the SERVICE stateless.
>
> <cite>Eric Evans</cite>

This Domain Service is defined in terms of the UBIQUITOUS LANGUAGE (e.g. `Purchase`, `PurchasesReport`). It is stateless because it just takes data and produce more data. This responsibility doesn't fit in any ENTITY:

```java
public class PurchasesReportGenerator {
    public PurchasesReport generate(List<Purchase> purchases) {
        ...
        // create a report based on purchases data
        ...
        return purchasesReport;
    }
}
```

## Infrastructure Services 
We might want to consult a third party API to store a report or sending an email. This is the kind of service that should be delegated to a Infrastructure Service.

In our case, what if we wanted to store a report somewhere and send an email notification when a new report is generated? We can have two Infrastructure Services to do all the hard work of exporting the report and sending an email.

This is an Application Service that coordinates a repository (`purchasesRepository`), a domain service (`PurchasesReportGenerator`) and two infrastructure services (`purchasesReportWriter` and `emailService`):

```java
public class GeneratePurchasesReport {
    ...
    public void execute(Date from, Date to) {
	
        List<Purchase> purchases = purchasesRepository.find(from, to);
        
        PurchasesReport purchasesReport = new PurchasesReportGenerator().generate(purchases);
        
        purchasesReportWriter.export(purchasesReport);
        
        emailService.sendReportGeneratedNotification(from, to);
    } 
}
```

An infrastructure service for exporting a report:

```java
public class PurchasesReportWriter {
    ...
    public void export(PurchasesReport purchasesReport) {
        // here we would have the code for adapting the report object to some format and writing it somewhere.
        System.out.println("Report exported in some format.");
    }
}
```

Another infrastructure service for sending email notifications:

```java
public class EmailService {
    ...
    public void sendReportGeneratedNotification(Date from, Date to) {
        // here we would have the code for sending an email.
        System.out.println("Purchases report generated. Range from " + from + " to " + to);
    } 
}
```

## Sources

### Value Objects

[Value Objects - Martin Flower bliki](https://martinfowler.com/bliki/ValueObject.html)

### Services

[Services in Domain-Driven Design (DDD) - Lev Gorodinski](http://gorodinski.com/blog/2012/04/14/services-in-domain-driven-design-ddd/)

[Services in Domain Driven Desing](https://lostechies.com/jimmybogard/2008/08/21/services-in-domain-driven-design/)

[Domain Services Vs Application Services](https://enterprisecraftsmanship.com/2016/09/08/domain-services-vs-application-services/)

### Model

[What is the Domain Model in Domain Driven Design?](https://www.culttt.com/2014/11/12/domain-model-domain-driven-design/)

[Domain Model - Martin Flower bliki](https://martinfowler.com/eaaCatalog/domainModel.html)

[Domain Model - Wikipedia](https://en.wikipedia.org/wiki/Domain_model)