###### Fizzbuzz


###### Nice solution:
```
package com.fizzbuzz;

import java.util.List;
import java.util.Map;
import java.util.Optional;

import static java.util.Arrays.stream;
import static java.util.function.Function.identity;
import static java.util.stream.Collectors.*;
import static org.apache.commons.lang3.StringUtils.isNumeric;


public class FizzBuzz {

    public static String fizzBuzz(Integer number) {
        return Optional.of(number).map(n -> {
            if (n.toString().contains("3")) return "lucky";
            else if (n % 15 == 0) return "fizzbuzz";
            else if (n % 3 == 0) return "fizz";
            else if (n % 5 == 0) return "buzz";
            else return n.toString();
        }).get();
    }

    public static String realFizzBuzz(List<Integer> list) {
        return list.stream()
                .map(number -> fizzBuzz(number))
                .collect(joining(" "));
    }

    public static Map<String, Long> frequencies(String output) {
        Map<String, Long> frequencies = stream(output.split(" "))
                .filter(w -> !isNumeric(w))
                .collect(groupingBy(identity(), counting()));
        frequencies.put("integer", stream(output.split(" "))
                .filter(w -> !isNumeric(w))
                .count());
        return frequencies;
    }
}
```


##### This is the test:

```
package com.fizzbuzz;

import org.junit.Test;

import java.util.List;
import java.util.Map;
import java.util.stream.Collectors;
import java.util.stream.IntStream;

import static com.fizzbuzz.FizzBuzz.fizzBuzz;
import static com.fizzbuzz.FizzBuzz.frequencies;
import static com.fizzbuzz.FizzBuzz.realFizzBuzz;
import static org.hamcrest.core.Is.is;
import static org.junit.Assert.assertThat;

public class FizzBuzzTest {

    private static final String EXPECTED_OUTPUT = "1 2 lucky 4 buzz fizz 7 8 fizz buzz 11 fizz lucky 14 fizzbuzz 16 17 fizz 19 buzz";

    @Test
    public void multiplesOf3_shouldReturnFizz() {
        assertThat(fizzBuzz(9), is("fizz"));
        assertThat(fizzBuzz(12), is("fizz"));
        assertThat(fizzBuzz(18), is("fizz"));
        assertThat(fizzBuzz(21), is("fizz"));
    }

    @Test
    public void multiplesOf5_shouldReturnBuzz() {
        assertThat(fizzBuzz(5), is("buzz"));
        assertThat(fizzBuzz(10), is("buzz"));
        assertThat(fizzBuzz(20), is("buzz"));
        assertThat(fizzBuzz(25), is("buzz"));
    }

    @Test
    public void multiplesOf15_shouldReturnFizzbuzz() {
        assertThat(fizzBuzz(15), is("fizzbuzz"));
        assertThat(fizzBuzz(45), is("fizzbuzz"));
        assertThat(fizzBuzz(60), is("fizzbuzz"));
        assertThat(fizzBuzz(75), is("fizzbuzz"));
    }

    @Test
    public void numberWithDigit3_shouldReturnLucky() {
        assertThat(fizzBuzz(3), is("lucky"));
        assertThat(fizzBuzz(13), is("lucky"));
        assertThat(fizzBuzz(23), is("lucky"));
        assertThat(fizzBuzz(30), is("lucky"));
    }

    @Test
    public void realFizzBuzzfrequencies_shouldBeSummedUp() {
        Map<String, Long> freq = frequencies(EXPECTED_OUTPUT);
        assertThat(freq.get("fizz"), is(4L));
        assertThat(freq.get("buzz"), is(3L));
        assertThat(freq.get("fizzbuzz"), is(1L));
        assertThat(freq.get("lucky"), is(2L));
        assertThat(freq.get("integer"), is(10L));
    }

    @Test
    public void realFizzBuzz_shouldReturnExpectedOutput() {
        List<Integer> list = IntStream.range(1,21).distinct().boxed().collect(Collectors.toList());
        assertThat(realFizzBuzz(list), is(EXPECTED_OUTPUT));
    }

}
```

##### Another solution:

```
package uk.co.compendiumdev.fizzbuzz;

public class FizzBuzzConverter {
    public String convert(int toConvertToFizzBuzz) {


        if(toConvertToFizzBuzz%15==0){
            return "FizzBuzz";
        }

        if(toConvertToFizzBuzz%5==0){
            return "Buzz";
        }

        if(toConvertToFizzBuzz%3==0){
            return "Fizz";
        }

        return String.valueOf(toConvertToFizzBuzz);
    }
}
```

##### Actual fizzbuzz:

```
import org.junit.Test;
import uk.co.compendiumdev.fizzbuzz.FizzBuzzConverter;

public class ActualFizzBuzzAppTest {


    @Test
    public void outputTheHundredFizzBuzzes(){

        FizzBuzzConverter fizzBuzz = new FizzBuzzConverter();
        for(int i=1; i<=100; i++){
            System.out.println(fizzBuzz.convert(i));
        }
    }
}
```

###### This was Ewan and my solution to FizzBuzz:

This was the test:
```
package com.codurance.fizzbuzz;

import org.junit.Before;
import org.junit.Test;

import static org.junit.Assert.assertEquals;

public class FizzBuzzShould {
  FizzBuzz fizzBuzz;
  @Before
  public void setUp() {
    fizzBuzz = new FizzBuzz();
  }

  @Test
  public void return_1_for_1() {
    assertEquals("1", fizzBuzz.convert(1));
  }

  @Test
  public void return_1_for_2() {
    assertEquals("2", fizzBuzz.convert(2));
  }

  @Test
  public void return_4_for_4() {
    assertEquals("4", fizzBuzz.convert(4));
  }

  @Test
  public void return_fizz_for_3() {
    assertEquals("fizz", fizzBuzz.convert(3));
  }

  @Test
  public void return_fizz_for_6() {
    assertEquals("fizz", fizzBuzz.convert(6));
  }

  @Test
  public void return_fizz_for_9() {
    assertEquals("fizz", fizzBuzz.convert(9));
  }

  @Test
  public void return_buzz_for_5() {
    assertEquals("buzz", fizzBuzz.convert(5));
  }

  @Test
  public void return_buzz_for_10() {
    assertEquals("buzz", fizzBuzz.convert(10));
  }

  @Test
  public void return_buzz_for_20() {
    assertEquals("buzz", fizzBuzz.convert(20));
  }

  @Test
  public void return_fizzbuzz_for_15() {
    assertEquals("fizzbuzz", fizzBuzz.convert(15));
  }

  @Test
  public void return_fizzbuzz_for_30() {
    assertEquals("fizzbuzz", fizzBuzz.convert(30));
  }
}
```
This included several tests on each multiple including numbers that we multiples of 3, 5 and numbers multiples of 15.

This is what the fizzbuzz looked like:

![Screenshot 2020-03-02 at 10 51 56](https://user-images.githubusercontent.com/27693622/75712387-c1e8f480-5cbf-11ea-9833-03485dc4f3de.png)


Working with Dmitri we did an extra functionality for Fizzbuzz:
```
One version of the FizzBuzz game in real life adds an extra Fizz or Buzz whenever one of the digits ('3' or '5') appears in the number itself (for example, see Dr Mike's Math Games for Kids).

So, '3' would become FizzFizz, '5' would become BuzzBuzz, '15' would be FizzBuzzBuzz.

Modify your program to reflect this requirement while maintaining your discipline in using red-green-refactor cycles.
```
https://katalyst.codurance.com/fizzbuzz

I was getting this error but I solved it with this:
https://github.com/junit-team/junit5/issues/1773
```
At first, I got the following error running the tests on IntelliJ and using the command mvn clean install:

[INFO] --- maven-surefire-plugin:3.0.0-M3:test (default-test) @ xxxxx ---
[INFO]
[INFO] -------------------------------------------------------
[INFO]  T E S T S
[INFO] -------------------------------------------------------
Feb 12, 2019 12:53:00 PM org.junit.platform.launcher.core.DefaultLauncher handleThrowable
WARNING: TestEngine with ID 'junit-jupiter' failed to execute tests
java.lang.NoSuchMethodError: org.junit.platform.commons.util.ReflectionUtils.tryToLoadClass(Ljava/lang/String;)Lorg/junit/platform/commons/function/Try;
        at org.junit.jupiter.engine.support.OpenTest4JAndJUnit4AwareThrowableCollector.createAbortedExecutionPredicate(OpenTest4JAndJUnit4AwareThrowableCollecto
After adding this dependency, I could run the tests in IntelliJ but not using Maven:

        <dependency>
            <groupId>org.junit.platform</groupId>
            <artifactId>junit-platform-launcher</artifactId>
            <version>1.4.0</version>
            <scope>test</scope>
        </dependency>
```

The FizzBuzz solution looked like this:
```
package com.codurance.fizz_buzz;

public class FizzBuzz {
  public String play(int number) {
    StringBuilder result = new StringBuilder();

    for (Value value : Value.values()){
      if (number % value.number == 0){
        result.append(value.response);
      }
    }

    for (Value value : Value.values()){
      if (String.valueOf(number).contains(String.valueOf(value.number))){
        result.append(value.response);
      }
    }

    if (result.length() == 0) {
      result.append(String.valueOf(number));
    }

    return result.toString();
  }

  private enum Value {
    THREE(3, "Fizz"),
    FIVE(5, "Buzz");

    public int number;
    public String response;

    Value(int number, String response) {
      this.number = number;
      this.response = response;
    }
  }
}
```
For this method we used a play method which took an int parameter:
```
    StringBuilder result = new StringBuilder();

    for (Value value : Value.values()){
      if (number % value.number == 0){
        result.append(value.response);
      }
    }
```
We then created a StringBuilder() and iterated over each value in Value.values(). The Value enum looked like the following:
```
private enum Value {
    THREE(3, "Fizz"),
    FIVE(5, "Buzz");

    public int number;
    public String response;

    Value(int number, String response) {
      this.number = number;
      this.response = response;
    }
  }
```
This had two values with number and response which could be accessed directly on the enum. In our for loop:
```
      if (number % value.number == 0){
        result.append(value.response);
      }
```
If the number parameter divided by value.number was equal to zero (ie the number was divisible by that number) we then appended the value.response (ie the string Fizz or Buzz) to the result StringBuilder().