##### Leap year Kata

###### These were the tests for my original implementation of leap year:

```
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;

import static junit.framework.Assert.assertEquals;


public class LeapYearShould {

    private LeapYear leapYear;

    @BeforeEach
    void setUp() {
        leapYear = new LeapYear();
    }

    @Test
    public void returnsTrueIfYearDivisibleByFour(){
        assertEquals(true, leapYear.isLeapYear(2020));
    }

    @Test
    public void returnsFalseIfYearNotDivisibleByFour(){
        assertEquals(false, leapYear.isLeapYear(2019));
    }

    @Test
    public void returnsFalseIfYearIsCenturyYearNotDivisibleBy400(){
        assertEquals(false, leapYear.isLeapYear(1900));
    }

    @Test
    public void returnsTrueIfYearIsCenturyYearDivisibleBy400(){
        assertEquals(true, leapYear.isLeapYear(2020));
    }
}
```
The tests for this kata were:
returns true if year divisible by four, return false if year not divisible by four, return false if year is century year not divisible by 400, return true if year is century year divisible by 400.

This was the code for the Kata:
```
public class LeapYear {
    public boolean isLeapYear(int year){
        return !(isNotGregorianLeapYear(year) || !isDivisibleByFour(year));
    }

    private boolean isNotGregorianLeapYear(int year){
        return isDivisibleBy100(year) && !isDivisibleByFourHundred(year);
    }

    private boolean isDivisibleByFour(int year){
        return year % 4 == 0;
    }

    private boolean isDivisibleByFourHundred(int year){
        return year % 400 == 0;
    }

    private boolean isDivisibleBy100(int year){
        return year % 100 == 0;
    }
}

```
The first check was to return not isNotGregorianLeapYear or not divisiblebyfour. The isNotGregorianLeapYear method was for:
```
return isDivisibleBy100(year) && !isDivisibleByFourHundred(year)
```
The isDivisibleBy100 method returns
```
year % 100 == 0
```
The isDivisbleByFourHundred is for:
```
return year % 400 == 0
```
The other check !isDivisibleByFourHundred(year) is for checking year % 400 == 0.


###### This was Sebastian and my solution:
This was the test:
```
import org.junit.jupiter.api.Test;

import static org.junit.jupiter.api.Assertions.assertFalse;
import static org.junit.jupiter.api.Assertions.assertTrue;


public class LeapYearShould {

    @Test
    void return_false_if_not_divisible_by_4() {
        assertFalse(LeapYear.check(405));
    }

    @Test
    void return_false_if_not_divisible_by_4_test_2() {
        assertFalse(LeapYear.check(407));
    }

    @Test
    void return_true_if_year_divisible_by_4() {
        assertTrue(LeapYear.check(404));
    }

    @Test
    void return_true_if_year_is_408() {
        assertTrue(LeapYear.check(408));
    }

    @Test
    void return_true_if_year_is_412() {
       assertTrue(LeapYear.check(412));
    }

    @Test
    void return_true_if_year_is_400() {
        assertTrue(LeapYear.check(400));
    }

    @Test
    void returns_false_if_year_is_500() {
        assertFalse(LeapYear.check(500));
    }
}
```
This was the code:
```
public class LeapYear {

    public static boolean check(int year){
        if (isDivisibleBy(year, 100) && !(isDivisibleBy(year, 400))){
            return false;
        }
        if (isDivisibleBy(year, 4)){
            return true;
        }
        return false;
    }

    private static boolean isDivisibleBy(int year, int factor) {
        return year % factor == 0;
    }
}
```
We had a separated method isDivisibleBy which checked if the number was divisible by a year and a factor which was entered in each use.
