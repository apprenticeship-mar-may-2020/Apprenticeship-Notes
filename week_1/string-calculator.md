##### String Calculator

These were the tests for the string calculator:
```
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.params.ParameterizedTest;
import org.junit.jupiter.params.provider.CsvSource;

import static junit.framework.Assert.assertEquals;

public class StringCalculatorShould {

    private StringCalculator stringCalculator;

    @BeforeEach
    void setUp() {
        stringCalculator = new StringCalculator();
    }

    @ParameterizedTest
    @CsvSource(value = {"!0",
                        "1! 1",
                        "2! 2",
                        "2,2! 4",
                        "1,2,3,4,5,6,7,8,9! 45"}, delimiter = '!')
    public void covertsStringToNumber(String stringOfNumbers, int result){
        assertEquals(result, stringCalculator.add(stringOfNumbers));
    }

    @Test
    public void convertsNewLineStringToNumber(){
        String stringOfNumbers = "1,2,3";
        int result = 6;
        assertEquals(result, stringCalculator.add(stringOfNumbers));
    }

    @Test
    void includesCustomSeparators() {
        String stringOfNumbers = "//;\n1;2";
        int result = 3;
        assertEquals(result, stringCalculator.add(stringOfNumbers));
    }

    @Test
    void throwsExceptionForNegatives() {
        String stringOfNumbers = "1,-2,-3";
        try{
            stringCalculator.add(stringOfNumbers);
        }catch(IllegalArgumentException e){
            assertEquals("Error: negatives not allowed: -2,-3", e.getMessage());
        }
    }

    @Test
    void ignoresNumbersGreaterThan1001() {
        String stringOfNumbers = "1001,2";
        assertEquals(2, stringCalculator.add(stringOfNumbers));
    }

    @Test
    void separatorsOfAnyLengthIfSurroundedBySquareBrackets() {
        String stringOfNumbers = "//[***]\n1***2***3";
        assertEquals(6, stringCalculator.add(stringOfNumbers));
    }

    @Test
    void handlesMultipleSeparatorsWithAnyCharacterLength(){
        String stringOfNumbers = "//[foo][bar]\n1foo2bar3";
        assertEquals(6, stringCalculator.add(stringOfNumbers));
    }
}
```

We used CsvSource for parameterized tests with this app. The CsvSource looked like this:
```
@CsvSource(value = {"!0",
                    "1! 1",
                    "2! 2",
                    "2,2! 4",
                    "1,2,3,4,5,6,7,8,9! 45"}, delimiter = '!')
```
This first test was only to check that normal numbers could be added (without whitespace etc). We added a delimiter="!" for this because we wanted to use , to separate the numbers in our parameterized test. We then checked for 3 numbers. We also checked for custom separators. Next we checked for exception for negatives and also ignored numbers greater than 1001. We then added separators of any length if the separators were surrounded by square brackets and finally we tested for multiple separators if they were separated by []. 

This was the code solution:
```
import java.util.ArrayList;
import java.util.List;

public class StringCalculator {
    public int add(String stringNumber) throws IllegalArgumentException {
        int result = 0;
        String separators = ",\n";
        List<String> negatives = new ArrayList<String>();

        if(stringNumber == null){
            return 0;
        }

        separators += format(stringNumber);

        String[] numbers = stringNumber.split("[" + separators + "]");

        for (String number : numbers) {
            if(number.matches(".*\\d.*")){
                int n = Integer.parseInt(number);
                if(n < 0){
                    negatives.add(number);
                }
                if(n < 1001){
                    result += n;
                }
            }
        }

        if(!negatives.isEmpty()){
            throw new IllegalArgumentException(
                    "Error: negatives not allowed: " + String.join(",", negatives)
            );
        }

        return result;
    }

    private String format(String stringNumber){
        String newSeparators = "";
        if(stringNumber.startsWith("//")){
            String[] array = stringNumber.split("\n");
            newSeparators = array[0].substring(2);

            if(newSeparators.contains("[")){
                String[] Separators = newSeparators.substring(1, newSeparators.length()-1).split("\\]\\[");
                newSeparators = String.join("|", Separators);
            }

        }
        return newSeparators;

    }

}
```
We included a stringNumber as the parameter for the method add. We added throw IllegalArgumentException if the negatives array was not empty. We also added a result = 0 and String separators ",\n" (the separators had comma and new line). We returned 0 if the number was null and then added the format(String) to the separators. Next we made an array of numbers splitting the stringNumber by the separators. 


##### Pair coding me and Alexis:
https://github.com/TomSpencerLondon/String-Calculator-Pairing-Alexis

Our tests looked like this:
```
package com.codurance.stringCalculator;

import static org.junit.Assert.assertEquals;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.params.ParameterizedTest;
import org.junit.jupiter.params.provider.CsvSource;

public class StringCalculatorShould {

    private StringCalculator stringCalculator;

    @BeforeEach
    public void setUp() {
        stringCalculator = new StringCalculator();
    }

    @Test
    public void empty_string_returns_0() {
        String number = "";
        assertEquals(0, stringCalculator.add(number));
    }

    @ParameterizedTest
    @CsvSource({
        "4,4",
        "5,5",
        "6,6"
    })
    public void single_digit_string_returns_integer(int number, String stringNumber) {
        assertEquals(number, stringCalculator.add(stringNumber));
    }


    @Test
    public void adds_strings_1_2_to_return_3() {
        String stringNumbers = "1,2";
        assertEquals(3, stringCalculator.add(stringNumbers));
    }

    @Test
    public void adds_strings_2_3_to_return_5() {
        String stringNumbers = "2,3";
        assertEquals(5, stringCalculator.add(stringNumbers));
    }

    @Test
    public void adds_strings_5_6_to_return_11() {
        String stringNumbers = "5,6";
        assertEquals(11, stringCalculator.add(stringNumbers));
    }


    @ParameterizedTest
    @CsvSource(value = {
        "1,2:3",
        "2,3:5",
        "5,6:11",
        "1,2,3,4,5,6,7,8,9:45",
    }, delimiter = ':')
    public void double_digit_string_returns_sum_as_integer(String stringNumber, int number) {
        assertEquals(number, stringCalculator.add(stringNumber));
    }

    @Test
    public void adds_strings_1_2_3_with_different_seperators() {
        assertEquals(6, stringCalculator.add(
            "1\n" +
            "2,3"));
    }

    @Test
    public void adds_strings_1_2_3_5_6_7_8_9_with_different_seperators() {
        assertEquals(45, stringCalculator.add("1\n2,3\n4,5\n6,7\n8,9"));
    }

    @Test
    public void adds_strings_1_2_with_custom_separator() {
        assertEquals(3, stringCalculator.add("//;\n1;2"));
    }

}
```

Here we used:
```
@ParameterizedTest
@CsvSource({
        "4,4",
        "5,5",
        "6,6"
    })
    public void single_digit_string_returns_integer(int number, String stringNumber) {
        assertEquals(number, stringCalculator.add(stringNumber));
    }
```
for parameterised tests. This included the number for the expected result and the stringNumber for the string that we were entering into the stringCalculator.add method. This is what our code so far looked like:
```
package com.codurance.stringCalculator;

public class StringCalculator {

    public static final String DEFAULT_DELIMITER = "[,\n]";

    public int add(String numbers) {
        String customSeparator = DEFAULT_DELIMITER;

        if (numbers.equals("")) {
            return 0;
        }

        if (numbers.startsWith("//")) {
            customSeparator = numbers.substring(2, numbers.indexOf("\n"));
            numbers = numbers.substring(numbers.indexOf("\n") + 1);
        }

        int sum = 0;
        for (String number : numbers.split(customSeparator)) {
            sum += Integer.parseInt(number);
        }
        return sum;
    }
}
```
It was nice that we had a DEFAULT_DELIMITER with , and \n for our delimiters. We also added an if statement with numbers.startsWith("//") to then add a custom separator based on the 2nd index of the numbers string with this list of delimiters finishing at the indexOf("\n"). We also used numbers.substring(numbers.indexOf("\n") + 1)) to ensure that we selected the numbers after \n for our numbers to iterate over.

This is what the code looked like in the retro:
![Screenshot 2020-03-02 at 17 00 29](https://user-images.githubusercontent.com/27693622/75712214-82220d00-5cbf-11ea-9d2f-3114c44aa6f8.png)


This was causing the Maven compileer issue - add this ensure that your compiler version is 12:
```
<properties>
    <maven.compiler.target>12</maven.compiler.target>
    <maven.compiler.source>12</maven.compiler.source>
</properties>
```

##### I looked at this refactoring series of lessons but it was in Csharp so not appropriate:
https://github.com/bnathyuw/Complex-Refactoring-In-Simple-Steps/tree/master/ReplaceMethodWithMethodObject/PostageCalculator/PostageCalculators

- This was the video series for the refactoring course in C sharp:
https://www.youtube.com/watch?v=ntjg6rE6lss
- I probably can do this but it is a bit difficult for the moment

###### This series of commits from Mars Rover command pattern article 

https://github.com/simion-iulian/mars-rover-article/commits/e532085ed71639a9098e2475748fddfd7d731a12?before=e532085ed71639a9098e2475748fddfd7d731a12+35

will be a very useful overview of the state and command patterns:
https://codurance.com/2019/01/22/mars-rover-kata-refactoring-to-patterns/


##### Arnaud mentioned lombok:
https://codurance.slack.com/archives/C1L17MPH7/p1581201484010800

It's been so long I didn't code in Java I thought a refresher would be a good thing. Because of that, I wanted to check some assumptions with people that actually code in Java and may have better practices:
  - Everybody I know hates Lombok, and I used to like it for the @value  annotation cause I hate the idea of implementing equals and hashcode. The library that I seem to need to do this job is immutables. Is this a recommended solution or is  there better alternatives?
 - what the best way to create a Java project from command line? I use to create from Maven artefact or with Gradle. Is this what people recommend?

