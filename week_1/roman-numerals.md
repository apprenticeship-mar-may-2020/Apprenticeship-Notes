###### Roman Numerals

The transformation priority premise was important for this kata:

```
The Transformations
So what are these transformations? Perhaps we can make a list of them:

({}–>nil) no code at all->code that employs nil
(nil->constant)
(constant->constant+) a simple constant to a more complex constant
(constant->scalar) replacing a constant with a variable or an argument
(statement->statements) adding more unconditional statements.
(unconditional->if) splitting the execution path
(scalar->array)
(array->container)
(statement->recursion)
(if->while)
(expression->function) replacing an expression with a function or algorithm
(variable->assignment) replacing the value of a variable.
```

##### This is the statement on RomanNumerals:
```
Transformation Priority Premise
Pedro Santos edited this page on 19 Oct 2017 
· 5 revisions
If we go back to how we evolve code in TDD, 
we have these methods:

Fake it is where you just return the exact 
value you need. If your test expects a zero 
from a method, use a “return 0;” statement. 
Usually you use this when you cannot tell how 
to implement certain functionality, or your 
previous steps were too large and you cannot 
figure out what went wrong. Something that 
works is better than something that doesn’t 
work!
Use obvious implementation when you are 
pretty sure of the code you need to write, so
write it and see if the test passes. The 
majority of the time you will use this method 
to move forward with TDD quickly.

Triangulation is where you want to generalize 
certain behaviour, but are not sure how to do 
it. So you start with fake it and add 
additional tests that force the code to be 
more generic along a certain dimension.

“Use obvious implementation” is ambiguous, 
what does “obvious implementation” mean? It
may mean something for you and something else 
for another developer. The transformations on
the following table are a way to clarify 
ambiguity the “obvious implementation”.
```
This was Sebastian and Alexis' solution:
![Screenshot 2020-03-03 at 10 48 51](https://user-images.githubusercontent.com/27693622/75776683-5862f780-5d4c-11ea-848a-3cc826cdad5f.png)

This was mine and Ewan's first solution:
![Screenshot 2020-03-03 at 10 55 47](https://user-images.githubusercontent.com/27693622/75776745-80525b00-5d4c-11ea-8e36-4bff17b473ea.png)
it was incorrect because it over used the for loops.

Sherlock mentioned about using repetition to move the algorithm forwards:
![Screenshot 2020-03-03 at 11 05 28](https://user-images.githubusercontent.com/27693622/75776838-ae379f80-5d4c-11ea-8413-3633b8853b56.png)

This was a nice statement of the Transformation Priority Premise:

![Screenshot 2020-03-03 at 11 08 53](https://user-images.githubusercontent.com/27693622/75776891-cb6c6e00-5d4c-11ea-8bc9-5b3ed29db79f.png)

This slide was a restatement of the TPP:
![Screenshot 2020-03-03 at 12 41 14](https://user-images.githubusercontent.com/27693622/75776969-f9ea4900-5d4c-11ea-8c7b-fc5b98de3d41.png)


This is the link for Roman Numerals:
https://katalyst.codurance.com/roman-numerals

For the purposes of practising TDD, this is one of the more advanced katas you can do. By now you should be proficient with the basic skills of TDD - the 3 Laws of TDD, the Rule of Three for duplication, and you should be experienced in how to structure a unit test (Arrange, Act, Assert).

This exercise allows you to refine the decision-making process for how you add tests. Done properly, you should be able to evolve the algorithm taking very small steps.

Doing it this way is a safe way to code: if you take a step that turns out as an unwise decision it is easier to reverse the step in your version control software and start anew. The ‘cost’ of doing this is minimal and it is generally easier to reverse and rethink than spending time debugging the code to find out what went wrong. Developers who are proficient in TDD generally spend a lot less time debugging code than simply opting out of their recent changes and trying another strategy.

The fast feedback you get from a small change means you can react immediately, rather than dealing with a problem that was introduced at some point previously where subsequent changes prior to the problem being detected can make it difficult to isolate. With small progressions, if you break something should very easy to isolate where you went wrong.

What’s recommended is that you support your decisions to progress using a heuristic such as the Transformation Priority Premise (see the "Articles" section below). This is an approach developed by Robert C. Martin (Uncle Bob) to help make TDD easier.

Transformations are best defined when compared to Refactorings. Refactorings are changes to the code that DO NOT change the code’s behaviour. Transformations are changes to the code that DO change the code’s behaviour, but in the context of TDD they are changes that allow the (current) failing test to pass, i.e. the code that is written to move the code from Red to Green.

In physics, the Second Law of Thermodynamics defines an arrow of progression where entropy moves from less to more. In TDD, there is an arrow of direction where the tests progress from being more specific to more generic. By defining the rules of Transformation Priority Premise, Uncle Bob has defined a series of ordered transformations. The transformations at the top of the list are the simplest. They become progressively more complex as you move down the list. By referencing and applying these transformation types, you can slowly and incrementally move your test suite from being specific to more generic.

This is a great exercise for practising test-driven algorithm design because you can start to familiarise yourself with using transformations to drive forward, but at the same time the direction of travel is not completely obscure. The rules for Roman Numerals (see below) would suggest a basic strategy, but you can try to use TPP to help pick the next step.

It’s relatively easy to know the next step is, but the point of the exercise is to try to let the TPP confirm your steps moving forward. It's advised to do this kata first choosing your own steps unaided, and then repeating it using the TPP to guide you.

This is the TPP:
https://github.com/codurance/apprenticeship/wiki/Transformation-Priority-Premise

This is the clean coder blog:
https://blog.cleancoder.com/uncle-bob/2013/05/27/TheTransformationPriorityPremise.html


This was our final test for the TPP:
```
import com.codurance.roman_numeral_converter.RomanNumeralConverter;
import org.junit.jupiter.params.ParameterizedTest;
import org.junit.jupiter.params.provider.CsvSource;

import static org.junit.Assert.assertEquals;

public class RomanNumeralConverterShould {
    @ParameterizedTest
    @CsvSource({
            "I,1",
            "II,2",
            "III,3",
            "V,5",
            "VI,6",
            "VII,7",
            "VIII,8",
            "X,10",
            "XX,20",
            "XXX,30",
            "XL,40",
            "L,50",
            "XC,90",
            "C,100",
            "D,500",
            "M,1000",
            "MI,1001"
    })
    public void converts_arabic_int_to_roman_string(String roman, int arabic) {
        RomanNumeralConverter converter = new RomanNumeralConverter();
        String converted = converter.arabicToRoman(arabic);
        assertEquals(roman, converted);
    }
}
```
We used Parameterised tests. We needed to add the junit-jupiter-params:
```
<project>
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.github.tomspencer</groupId>
  <artifactId>roman_numerals</artifactId>
  <version>1</version>
  <dependencies>
    <dependency>
      <groupId>org.apache.commons</groupId>
      <artifactId>commons-lang3</artifactId>
      <version>3.7</version>
    </dependency>
    <dependency>
      <groupId>org.junit.jupiter</groupId>
      <artifactId>junit-jupiter-api</artifactId>
      <version>5.6.0</version>
    </dependency>
    <dependency>
      <groupId>org.junit.jupiter</groupId>
      <artifactId>junit-jupiter-params</artifactId>
      <version>5.6.0</version>
    </dependency>
    <dependency>
      <groupId>org.easytesting</groupId>
      <artifactId>fest-assert</artifactId>
      <version>1.4</version>
    </dependency>
    <dependency>
      <groupId>org.mockito</groupId>
      <artifactId>mockito-all</artifactId>
      <version>1.10.19</version>
    </dependency>
    <dependency>
      <groupId>pl.pragmatists</groupId>
      <artifactId>JUnitParams</artifactId>
      <version>1.1.0</version>
    </dependency>
  </dependencies></project>
```
This is the junit jupiter params:
```
<dependency>
  <groupId>org.junit.jupiter</groupId>
  <artifactId>junit-jupiter-params</artifactId>
  <version>5.6.0</version>
</dependency>
```
This is what our final solution looked like:
```
package com.codurance.roman_numeral_converter;

public class RomanNumeralConverter {
    public String arabicToRoman(int arabic) {
        String roman = "";

        for (romanNumerals numeral : romanNumerals.values()){
            while (arabic >= numeral.arabic) {
                roman += numeral.roman;
                arabic -= numeral.arabic;
            }
        }

        return roman;
    }

    private enum romanNumerals {
        ONE_THOUSAND("M", 1000),
        FIVE_HUNDRED("D", 500),
        ONE_HUNDRED("C", 100),
        NINETY("XC", 90),
        FIFTY("L", 50),
        FORTY("XL", 40),
        TEN ("X", 10),
        FIVE ("V", 5),
        ONE ("I", 1);

        public String roman;
        public int arabic;

        romanNumerals(String roman, int arabic) {
            this.roman = roman;
            this.arabic = arabic;
        }
    }
}
```
We used an enum to store the RomanNumerals. This is in order so we can use this to iterate over the keys and values. We do this through a for loop:

```
for (romanNumerals numeral : romanNumerals.values()){
    while (arabic >= numeral.arabic) {
        roman += numeral.roman;
        arabic -= numeral.arabic;
    }
}
```
Here we iterate over the romanNumerals.values with a for each loop. This allows us to check that the arabic number is above numeral.arabic and while this is the case it then adds the numeral.roman to the string and then takes away numeral.arabic from the arabic parameter.

This was the wiki on TPP:
https://github.com/codurance/apprenticeship/wiki/Transformation-Priority-Premise

This was the pairing matrix:
https://docs.google.com/spreadsheets/d/15hgQxki9I_mUPKDKDV2HEVe-7kGoX5MMu0Bm201G-E0/edit?ts=5e5cdd36#gid=0

I hadn't understood that we didn't need to add the number to the shaded cells:
![Screenshot 2020-03-03 at 13 14 07](https://user-images.githubusercontent.com/27693622/75779097-f1940d00-5d50-11ea-962d-0643e9da0320.png)
otherwise the cells would be duplicated.

##### This is an example of recursion with the RomanNumeral kata:

```
const nums = {I: 1, V: 5, X: 10, L: 50, C: 100, M: 1000};

const arabify = (romNum) => {
    if (romNum.length === 0) {
        return 0;
    } else if (nums[romNum[0]] < nums[romNum[1]]) {
        return (nums[romNum[1]] - nums[romNum[0]] + arabify(romNum.slice(2)));
    } else {
        return (nums[romNum[0]] + arabify(romNum.slice(1)));
    }
}
```

##### This is an interesting response that uses recursion:
![Screenshot 2020-03-03 at 13 18 06](https://user-images.githubusercontent.com/27693622/75779485-96aee580-5d51-11ea-9b77-7155d874f034.png)

This used recursion because it had a terminate condition.



![Screenshot 2020-03-03 at 15 12 25](https://user-images.githubusercontent.com/27693622/75861625-8ea88180-5df5-11ea-8a91-4030f7681324.png)


![Screenshot 2020-03-03 at 15 13 58](https://user-images.githubusercontent.com/27693622/75861673-a3851500-5df5-11ea-95e6-199d63332a52.png)


![Screenshot 2020-03-03 at 15 18 20](https://user-images.githubusercontent.com/27693622/75861706-ac75e680-5df5-11ea-9ad3-48a0e10249d9.png)


