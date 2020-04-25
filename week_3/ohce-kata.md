##### Ohce kata
Our solution for this kata was at:
https://github.com/TomSpencerLondon/ohce-mocking-practice/blob/master/src/main/java/com/codurance/ohce/Ohce.java

This was the implementation of the Ohce Kata that Alexis and I worked on:
https://kata-log.rocks/ohce-kata

This was what the Ohce kata looked like:
```
Description
ohce is a console application that echoes the reverse of what you input through the console.

Even though it seems a silly application, ohce knows a thing or two.

When you start oche, it greets you differently depending on the current time, but only in Spanish:
Between 20 and 6 hours, ohce will greet you saying: ¡Buenas noches < your name >!
Between 6 and 12 hours, ohce will greet you saying: ¡Buenos días < your name >!
Between 12 and 20 hours, ohce will greet you saying: ¡Buenas tardes < your name >!
When you introduce a palindrome, ohce likes it and after reverse-echoing it, it adds ¡Bonita palabra!
ohce knows when to stop, you just have to write Stop! and it'll answer Adios < your name > and end.
This is an example of using ohce during the morning:

$ ohce Pedro
> ¡Buenos días Pedro!
$ hola
> aloh
$ oto
> oto
> ¡Bonita palabra!
$ stop
> pots
$ Stop!
> Adios Pedro
```
This is what the test looked like:
```
package com.codurance.ohce;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;

import java.util.Calendar;

import static org.junit.jupiter.api.Assertions.assertEquals;

public class OhceShould {
  private TestableConsole testableConsole;
  private Ohce ohce;

  private TestableClock testableClock;
  private int HOUR;

  @BeforeEach
  void setUp() {
    testableConsole = new TestableConsole();
    testableClock = new TestableClock();
    ohce = new Ohce(testableConsole, testableClock);
  }

  @Test
  void reverses_a_string() {
    ohce.echo("hola");
    assertEquals("aloh", testableConsole.getText());
  }

  @Test
  void adds_bonita_palabra_for_palindome() {
    ohce.echo("oto");
    assertEquals("oto\n¡Bonita palabra!", testableConsole.getText());
  }

  @Test
  void returns_buenas_noches() {
    HOUR = 5;

    ohce.echo("ohce Pedro");
    assertEquals("¡Buenas noches Pedro!", testableConsole.getText());
  }

  @Test
  void returns_Buenos_días() {
    HOUR = 7;

    ohce.echo("ohce Pedro");
    assertEquals("¡Buenos días Pedro!", testableConsole.getText());
  }

  private class TestableConsole implements Console {

    private String text;

    @Override
    public void print(String text) {
      this.text = text;
    }

    public String getText() {
      return text;
    }
  }

  private class TestableClock implements Clock {
    @Override
    public Calendar currentTime() {
      Calendar time = Calendar.getInstance();
      time.set(Calendar.HOUR_OF_DAY, HOUR);
      return time;
    }
  }
}
```
We first tested for reversing the string:
```
  @Test
  void reverses_a_string() {
    ohce.echo("hola");
    assertEquals("aloh", testableConsole.getText());
  }
```
We implemented this through a StringBuilder and then calling reverse() on the StringBuilder:
```
public void echo(String text) {
        StringBuilder result = new StringBuilder(text).reverse();

        if (isGreet(text)) {
            result = greet(text);
        }

        if (isPalindrome(text)) {
            result.append(NEW_LINE + PALINDROME_MSG);
        }

        console.print(result.toString());
    }
```
This meant that we could use the reverse() method. We then added the bonita_palabra message for a palindrome:
```
  @Test
  void adds_bonita_palabra_for_palindome() {
    ohce.echo("oto");
    assertEquals("oto\n¡Bonita palabra!", testableConsole.getText());
  }

```
For both these tests we used a TestableConsole to implement that particular message of the Console:
```
  private class TestableConsole implements Console {

    private String text;

    @Override
    public void print(String text) {
      this.text = text;
    }

    public String getText() {
      return text;
    }
  }
```
This was interesting because we could override the methods from the Interface such as print and then add a getText() method in order to fetch the text from the class. The next part of the application was a little bit more difficult and involved implementing the greetings for different times of the day:
```

  @Test
  void returns_buenas_noches() {
    HOUR = 5;

    ohce.echo("ohce Pedro");
    assertEquals("¡Buenas noches Pedro!", testableConsole.getText());
  }

  @Test
  void returns_Buenos_días() {
    HOUR = 7;

    ohce.echo("ohce Pedro");
    assertEquals("¡Buenos días Pedro!", testableConsole.getText());
  }
```
for these methods we used a testableClock in the constructor of our Ohce and then used the field HOUR to set the hour for the currentTime method:
```
  private class TestableClock implements Clock {
    @Override
    public Calendar currentTime() {
      Calendar time = Calendar.getInstance();
      time.set(Calendar.HOUR_OF_DAY, HOUR);
      return time;
    }
  }
```
This was good because we were able to set the time by using getInstance class method on Calendar. We then called:
```
time.set(Calendar.HOUR_OF_DAY, HOUR);
```
to set the hour for time and then returned this from our currentTime() method. The one error we did make was to try to implement all the logic for the morning and evening greetings to the Ohce class:
```
private StringBuilder greet(String text) {
        StringBuilder stringBuilder = new StringBuilder();
        String name = text.split(" ")[1];

        if (isMorning()) {
            stringBuilder.append(MORNING_GREETING);
        } else {
            stringBuilder.append(EVENING_GREETING);
        }

        return stringBuilder.append(name)
                            .append("!");
    }

    private boolean isMorning() {
        Calendar now = clock.currentTime();
        firstBoundary.set(Calendar.HOUR_OF_DAY, 6);
        secondBoundary.set(Calendar.HOUR_OF_DAY, 12);
        return now.after(firstBoundary) && now.before(secondBoundary);
    }

    private boolean isGreet(String text) {
        return text.split(GREET_SEPERATOR)[0].equals(GREET_MESSAGE);
    }

    private boolean isPalindrome(String text) {
        return text.equals(new StringBuilder(text).reverse().toString());
    }
```
A better design solution would have been to extract this to the Clock class. In our current implementation we used the StringBuilder to append the MORNING_GREETING or the EVENING_GREETING depending on whether the time was morning (isMorning()). The check for isMorning() needed to set the first boundary and lastBoundary for the times and then check whether the time from the clock was after the firstBoundary and before the second boundary. Luckily we had access to .after and .before on clock.currentTime() form the Calendar.getInstance(). This made the method a bit easier but still left some long winded code for the isGreet method. This isGreet took the first part of the text and checked if it included "ohce" which was our signal to show that it was a greeting we would then use the stringBuilder to append the name.