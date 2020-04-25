###### Code Smells

This is the code smells website:
https://sourcemaking.com/refactoring/smells

![Screenshot 2020-03-01 at 12 30 25](https://user-images.githubusercontent.com/27693622/75626647-22ddd300-5bc1-11ea-9863-715ea62b01f6.png)

This is from week 1:
https://github.com/codurance/apprenticeship/wiki/Week-1

This is the codesmells info page:
https://github.com/codurance/apprenticeship/wiki/Code-Smells

##### This is the list of code smells:
```
Code Smells
Sam Davies edited this page on 30 Aug 2018 · 17 revisions
Duplicated Code
Identical or very similar code exists in more than one location or duplicated knowledge.
DRY violation.
Cohesion violation.

Long Method
Methods should do only one thing.
SRP violation. “Functions should do one thing. Should do it well.
Should do it only”.
One level of abstraction. No more than 5 to 15 lines.
Cohesion violation.

Large Class
Classes should have only one responsibility.
No more than 50 lines per class.
Possible SRP violation.
Cohesion violation.

Long Parameter List
Ideal: 0 niladic
Ok: 1 monadic
Acceptable: 2 dyadic
Debatable: 3 triadic avoid
Special justification: 3+ polyadic.
Coupling violation.
Connascence of position.

Divergent Change
One class is commonly changed in different ways for different reasons.
OCP, SRP violation.
God class.
Cohesion violation.

Shotgun Surgery
Opposite of Divergent change. One change, forces lots of little changes in different classes.
DRY violation.
Coupling violation.

Feature Envy
A class that uses methods or properties of another class excessively.
Tell, don’t ask violation.
Cohesion and Coupling violation.

Data Clumps
Same data items together in lots of places.
Special case of duplicated code. DRY violation.
Cohesion violation.

Primitive Obsession
Don't use primitive types as substitutes for classes. If the data type is sufficiently complex, use a class to represent it.
Coupling violation.
Connascence of meaning.

Switch Statements
Can lead to same switch statement scattered about a program in different places.
Can lead to Dry violation.
Could be Cohesion violation.

Parallel Inheritance Hierarchies
Special case of shotgun surgery. Creating a subclass of one class, forces subclass of another.

Data Class
Classes that have fields, properties, and nothing else.
Anaemic classes that contain no behaviour. Special case of lazy class.
Cohesion violation.

Speculative Generality
YAGNI violation or only users of method or class are test cases.
Cohesion violation.

Temporary Field
Class contains an instance variable set only in certain circumstances.
Cohesion violation.

Message Chains
Too many dots: Dog.Body.Tail.Wag()
Should be:
Dog.ExpressHappiness()
Law of Demeter violation.
Coupling violation.

Middle Man
If a class is delegating all its work, cut out the middleman.
Beware classes that are wrappers over other classes or existing functionality.
Special case of lazy class.

Inappropriate Intimacy
A class that has dependencies on implementation details of another class.
Special case of feature envy.
Cohesion violation.

Alternative Classes with Different Interfaces
If two classes are similar on the inside, but different on the outside, perhaps they can be modified to share a common interface.

Incomplete Library Class
Adding missing functionality by not changing library can lead to functionality implemented in odd places.

Lazy Class
A class that does too little.
May be acting only as middle man or a data class or can be caused by speculative generality.

Refused Bequest
Throw not implemented. Usually means the hierarchy is wrong.
LSP violation.

Comments
Make effort to create code that expresses intent instead of comments.

Dead Code
Code that has no references to it, commented code.

```