###### Object Calisthenics - Jeff Bay
http://www.bennadel.com/resources/uploads/2012/ObjectCalisthenics.pdf

9 steps to better software design today
1. One level of indentation per method
2. Don't use the ELSE keyword
3. Wrap all primitives and Strings
4. First class collections
5. One dot per line
6. Don't abbreviate
7. Keep all entities small
8. No classes with more than two instance variables
9. No getters/setters/properties


1. One level of indentation per method

![Screenshot 2020-03-04 at 17 47 11](https://user-images.githubusercontent.com/27693622/75907441-35197480-5e40-11ea-9a70-38e087dc1a04.png)

2. Don’t use the ELSE keyword
 Early Return: for parameter validation return immediately
 Map
 Polymorphism: is an alternative to if/else/switch statements. For instance,
it is possible to use Strategy Pattern or inheritence to replace every clause
in the control statement.
There are variations to how the "right" strategy is obtained.
– caller provides the strategy (as a method argument)
– strategy is memorised as member variable
– strategy is obtained from a map (or any other lookup mechanism)
 Null Object or Optional or Empty list : Dramatically reduces the need for
null checking.

3. Wrap all primitives and Strings

![Screenshot 2020-03-04 at 17 48 17](https://user-images.githubusercontent.com/27693622/75907529-59755100-5e40-11ea-95dd-e1745ac02f02.png)

4. First class collections
 Same as 3.Wrap all primitives but for collections
 Behaviors related to the collection have a home

![Screenshot 2020-03-04 at 17 48 54](https://user-images.githubusercontent.com/27693622/75907570-70b43e80-5e40-11ea-818c-af584c6038e0.png)

5. One dot per line
 Law of Demeter (“Only talk to your friends”)

6. Don’t abbreviate
 Keep class and method names to 1-2 words, and avoid names that
duplicate the context
Think about why you want to abbreviate. Is it because you’re typing the same word over
and over again? If that’s the case, perhaps your method is used too heavily and you are
missing opportunities to remove duplication. Is it because your method names are getting
long? This might be a sign of a misplaced responsibility, or a missing class.

![Screenshot 2020-03-04 at 17 50 17](https://user-images.githubusercontent.com/27693622/75907682-a0fbdd00-5e40-11ea-8c49-fc800d4765a3.png)

7. Keep all entities small
 No class over 50 lines
 No package over 10 files
 SRP (Single Responsibility Principle)
