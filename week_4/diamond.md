###### Diamond

This was my solution:

```

class Scratch {
  public static void main(String[] args) {
    System.out.println(print('L'));
  }

  public static String print(char letter){
    int interval = letter - 'A';
    int letterA = (int)'A';

    ArrayList<Character> alphabet = new ArrayList<>();

    for (int i = 0; i <= interval; i++) {
      alphabet.add((char)(letterA + i));
    }

    ArrayList<String> rows = new ArrayList<>();

    StringBuilder result = new StringBuilder();
    for (int i = 0; i < alphabet.size(); i++) {
      if (i == 0){
        result.append(dashes(interval - i) + alphabet.get(i) + dashes(interval - i));
        result.append("\n");
      }
      else {
        result.append(dashes(interval - i) + alphabet.get(i) + dashes(
                (i * 2) - 1) + alphabet.get(i) + dashes(interval - i));
        result.append("\n");
      }
    }
    String[] r = result.toString().split("\n");
    List<String> mirror = new ArrayList<String>(asList(r));
    reverse(mirror);
    mirror.remove(0);

    String mirrored = String.join("\n", mirror);

    return result.append(mirrored).toString();
  }

  public static String dashes(int n){
    String dashes = "";
    for (int i = 0; i < n; i++) {
      dashes += "-";
    }
    return dashes;
  }

```
This starts by creating an interval integer and a variable for the letter A:
```
    int interval = letter - 'A';
    int letterA = (int)'A';
```
This allows us to iterate incrementally through the letters A-Z. A is unicode: 66.

We Then create an ArrayList with the alphabet:
```
    ArrayList<Character> alphabet = new ArrayList<>();

    for (int i = 0; i <= interval; i++) {
      alphabet.add((char)(letterA + i));
    }
```
This creates a new alphabet array list and then adds characters up until the letter given in what I will eventually call the lettersUpTo method. 

We then create a rows ArrayList:
```
    ArrayList<String> rows = new ArrayList<>();

```
and a result StringBuilder:
```
StringBuilder result = new StringBuilder();
```
This is the rest of the class:
```

    StringBuilder result = new StringBuilder();
    for (int i = 0; i < alphabet.size(); i++) {
      if (i == 0){
        result.append(dashes(interval - i) + alphabet.get(i) + dashes(interval - i));
        result.append("\n");
      }
      else {
        result.append(dashes(interval - i) + alphabet.get(i) + dashes(
                (i * 2) - 1) + alphabet.get(i) + dashes(interval - i));
        result.append("\n");
      }
    }
    String[] r = result.toString().split("\n");
    List<String> mirror = new ArrayList<String>(asList(r));
    reverse(mirror);
    mirror.remove(0);

    String mirrored = String.join("\n", mirror);

    return result.append(mirrored).toString();
  }

  public static String dashes(int n){
    String dashes = "";
    for (int i = 0; i < n; i++) {
      dashes += "-";
    }
    return dashes;
  }
```
We create a StringBuilder and then iterate over the alphabet to append dashes interval - i, alphabet.get(i) + dashes interval - i and "\n" for the A and then append dashes(interval - i) alphabet.get(i) + dashes((i * 2) - 1) + alphabet.get(i) + dashes(interval - i).

We then create an array list from the result.toString().split("\n") and remove(0). Then we append the mirrored to String. 

The dashes method iterates over the integer and then adds dashes to a String.

![Screenshot 2020-03-25 at 23 38 30](https://user-images.githubusercontent.com/27693622/77631308-4b4cb900-6f44-11ea-9682-d654f52199b2.png)


This was Sandro Mancuso's solution:

```
package com.codurance;

import java.util.ArrayList;
import java.util.List;

import static java.util.Collections.reverse;
import static org.apache.commons.lang.StringUtils.repeat;

public class Diamond {

	public static final char START_LETTER = 'A';

	public static String[] upTo(char stopLetter) {
		List<String> rows = createRows(stopLetter);
		List<String> mirroredRows = mirrorRows(rows);
		rows.addAll(mirroredRows);

		return rows.toArray(new String[rows.size()]);
	}

	private static List<String> mirrorRows(List<String> rows) {
		List<String> mirroredRows = new ArrayList<>(rows);
		reverse(mirroredRows);
		mirroredRows.remove(0);
		return mirroredRows;
	}

	private static List<String> createRows(char stopLetter) {
		List<String> rows = new ArrayList<>();
		int letterInterval = stopLetter - START_LETTER;
		for (int letterIndex = 0; letterIndex <= letterInterval; letterIndex++) {
			rows.add(createRow(stopLetter, letterIndex));
		}
		return rows;
	}

	private static String createRow(char stopLetter, int letterIndex) {
		char currentLetter = (char)(START_LETTER + letterIndex);
		String outerDashes = dashes(stopLetter - currentLetter);

		String row = outerDashes + currentLetter + innerDashes(currentLetter);
		if (letterIndex > 0) row += currentLetter;
		row += outerDashes;

		return row;
	}

	private static String innerDashes(int currentLetter) {
		int lettersInterval = currentLetter - START_LETTER;
		return dashes((lettersInterval * 2) - 1);
	}

	private static String dashes(int number) {
		return repeat("-", number);
	}

}

```

This was his test:
https://github.com/sandromancuso/diamond_kata/blob/master/src/test/java/com/codurance/DiamondShould.java
```
package com.codurance;

import org.junit.Test;

import static org.hamcrest.CoreMatchers.is;
import static org.junit.Assert.assertThat;

public class DiamondShould {

	@Test public void
	create_diamond_with_single_row_A() {
		String[] diamond = Diamond.upTo('A');

		assertThat(diamond[0], is("A"));
	}

	@Test public void
	have_one_dash_on_each_side_of_A_when_up_to_B() {
		String[] diamond = Diamond.upTo('B');

		assertThat(diamond[0], is("-A-"));
	}

	@Test public void
	have_two_dashes_on_each_side_of_A_when_up_to_C() {
		String[] diamond = Diamond.upTo('C');

		assertThat(diamond[0], is("--A--"));
	}

	@Test public void
	have_B_in_the_second_row() {
		String[] diamond = Diamond.upTo('B');

	    assertThat(diamond[1], is("B-B"));
	}

	@Test public void
	have_C_in_the_third_row() {
		String[] diamond = Diamond.upTo('C');

	    assertThat(diamond[2], is("C---C"));
	}

	@Test public void
	have_D_in_the_fourth_row() {
		String[] diamond = Diamond.upTo('D');

	    assertThat(diamond[3], is("D-----D"));
	}

	@Test public void
	have_B_row_when_stop_letter_is_C() {
		String[] diamond = Diamond.upTo('C');

		assertThat(diamond[1], is("-B-B-"));
	}

	@Test public void
	have_B_row_when_stop_letter_is_D() {
		String[] diamond = Diamond.upTo('D');

		assertThat(diamond[1], is("--B-B--"));
	}

	@Test public void
	have_C_row_when_stop_letter_is_D() {
		String[] diamond = Diamond.upTo('D');

		assertThat(diamond[2], is("-C---C-"));
	}

	@Test public void
	have_rows_from_A_to_D() {
		String[] diamond = Diamond.upTo('D');

		assertThat(diamond[0], is("---A---"));
		assertThat(diamond[1], is("--B-B--"));
		assertThat(diamond[2], is("-C---C-"));
		assertThat(diamond[3], is("D-----D"));
	}

	@Test public void
	have_mirrored_rows_at_the_bottom() {
		String[] diamond = Diamond.upTo('D');

		assertThat(diamond[0], is("---A---"));
		assertThat(diamond[1], is("--B-B--"));
		assertThat(diamond[2], is("-C---C-"));
		assertThat(diamond[3], is("D-----D"));
		assertThat(diamond[4], is("-C---C-"));
		assertThat(diamond[5], is("--B-B--"));
		assertThat(diamond[6], is("---A---"));
	}

}
```


This may be relevant for testing:
https://github.com/sandromancuso/Bank-kata/blob/master/src/test/java/org/craftedsw/acceptancetests/steps/StatementPrintingSteps.java

```

	@Then("she would see $statement")
	public void thenSheWouldSee(String statement){
		InOrder inOrder = Mockito.inOrder(printer);
		inOrder.verify(printer).println("date       | credit   | debit    | balance");
		inOrder.verify(printer).println("14/01/2012 |          | 500.00   | 2500.00");
		inOrder.verify(printer).println("13/01/2012 | 2000.00  |          | 3000.00");
		inOrder.verify(printer).println("10/01/2012 | 1000.00  |          | 1000.00");
	}
```
###### This is Ron Jeffries who wrote about this kata:
https://twitter.com/RonJeffries

This is his article on the Diamond kata:
https://ronjeffries.com/articles/tdd-diamond/

We use Collections.reverse to reverse an ArrayList:
https://javahungry.blogspot.com/2017/09/how-to-reverse-arraylist-in-java-with-Example.html
```
import java.util.*;
 
public class ReverseArrayList
{
    public static void main(String[] args) 
    {
        //Creating an ArrayList object         
        ArrayList<String> arrlist = new ArrayList<String>(); 
 
        //Adding elements to ArrayList object 
        arrlist.add("Apple");         
        arrlist.add("Amazon");        
        arrlist.add("Facebook");
        arrlist.add("Google");
        arrlist.add("IBM");         
        arrlist.add("Tesla");
                 
        //Displaying ArrayList Before Reverse         
        System.out.println("Before Reverse ArrayList:");         
        System.out.println(arrlist);
         
        /*Reversing the list using 
          Collections.reverse() method*/         
        Collections.reverse(arrlist);
         
        //Displaying list after reverse         
        System.out.println("After Reverse ArrayList:");         
        System.out.println(arrlist);
    }
} 
```

This is for reverse() with StringBuilder:
https://www.geeksforgeeks.org/reverse-a-string-in-java/
```
// Java program to ReverseString using StringBuilder 
import java.lang.*; 
import java.io.*; 
import java.util.*; 
  
// Class of ReverseString 
class ReverseString 
{ 
    public static void main(String[] args) 
    { 
        String input = "Geeks for Geeks"; 
  
        StringBuilder input1 = new StringBuilder(); 
  
        // append a string into StringBuilder input1 
        input1.append(input); 
  
        // reverse StringBuilder input1 
        input1 = input1.reverse(); 
  
        // print reversed String 
        System.out.println(input1); 
    } 
} 
```

Remove last element in the Array:
https://www.google.com/search?q=delete+last+element+from+array+java&rlz=1C5CHFA_enGB834GB834&oq=delete+last+element+from+array+java&aqs=chrome..69i57j0l7.9743j0j7&sourceid=chrome&ie=UTF-8
```
We can use the remove() method of ArrayList container in Java to remove the last element. ArrayList provides two overloaded remove() method: remove(int index) : Accept index of the object to be removed. We can pass the last elements index to the remove() method to delete the last element.
```

##### This was useful for creating an ArrayList from a string:
https://beginnersbook.com/2015/05/java-string-to-arraylist-conversion/

```
import java.util.ArrayList;
import java.util.List;
import java.util.Arrays;
public class JavaExample {
    public static void main(String args[]){
	String num = "22,33,44,55,66,77";
	String str[] = num.split(",");
	List<String> al = new ArrayList<String>();
	al = Arrays.asList(str);
	for(String s: al){
	   System.out.println(s);
	}
   }
}
```
Here we use Array.asList() on str String str[]. This was created from calling.split("") on the string.

This was useful for Shortcuts for IntelliJ on Windows:
https://www.cloudflight.io/press/intellij-idea-eclipse-shortcuts-1689/

###### This was useful for checking if an array contains two elements:
https://stackoverflow.com/questions/20755432/how-to-check-if-an-arraylist-contain-2-values
```
person.toString().matches(".*\\b(Joe|Jasha)\\b.*");
```

I preferred .containsAll on this same site:
```
Simply use Arrays class

person.containsAll(Arrays.asList("Jasha","Joe"));
```

This was interesting on TDD with algorithms:
https://softwareengineering.stackexchange.com/questions/339855/tdd-like-approach-to-algorithmic-problems
```
See also Ron Jeffries's attempt to create a Sudoku solver with TDD, which unfortunately didn't work.

Algorithm requires a significant understanding of algorithm design principles. With these principles it is indeed possible to proceed incrementally, with a plan, like Peter Norvig did.

In fact, for algorithms requiring non-trivial design effort, it is almost always that the effort is incremental in nature. But each "increment", which is tiny in the eyes of an algorithm designer, looks like a quantum leap (to borrow your phrase) to a person who hasn't had the same expertise or knowledge with this particular family of algorithms.

This is why a basic education in CS theory combined with lots of algorithm programming practice are equally important. Knowing that a particular "technique" (small building blocks of algorithms) exists is a long way toward making these incremental quantum leaps.

```

This is a very interesting debate between Ron Jeffries and Peter Norvig on value of TDD:
https://www.infoq.com/news/2007/05/tdd-sudoku/

This is Norvig:
```
In this essay I tackle the problem of solving every Sudoku puzzle. It turns out to be quite easy (about 100 lines of [Python] code) using two ideas: constraint propagation and search.
```
This is Ron Jeffries with TDD (less directed):
```
I could start, I suppose, with a simple Sudoku game and try to TDD its solution. I don't feel that I could do that... So, instead, I'm going to set up a game with an assumed internal data structure, and then TDD some methods that I expect to need... Arguably this is a violation of YAGNI, but since I don't really know how to start or how to know when I'm done, writing some Spike code in TDD style seems to me to be a good way to get my feet wet.
I'm not saying this is good, or what you should do, or anything of the kind. I'm displaying what I do, faced with this problem, and how I explore what the computer and I can do in moving toward a Suduko solution.
```
In other words, Norvig's essay presents a logical progression of ideas towards a compact, elegant solution ("science as it's shown"), whereas Jeffries' series of articles give insight into the actual discovery process ("science as it's done").

This was Alexis' solution ages ago:
https://github.com/apavlidi/Code-Kata/blob/master/src/org/apavlidi/diamond/Diamond.java


This was my solution in the peer coding session:
https://github.com/TomSpencerLondon/Diamond-Kata-Peer-review

This was the test:
```
package com.codurance.diamond;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;

import java.util.Arrays;
import java.util.List;

import static org.junit.jupiter.api.Assertions.assertEquals;

public class DiamondShould {

  private Diamond diamond;

  @BeforeEach
  void setUp() {
    diamond = new Diamond();
  }

  @Test
  void print_A_for_A() {
    List<String> result = diamond.lettersUpTo('A');
    assertEquals("A", result.get(0));
  }

  @Test
  void print_diamond_A_B_A_for_B() {
    List<String> result = diamond.lettersUpTo('C');
    List<String> output = Arrays.asList("-A-", "B-B", "-A-");
    assertEquals(output, result);
  }

//
//  @Disabled
//  @Test
//  void print_diamond_A_B_C_B_A_for_C() {
//    List<String> result = diamond.lettersUpTo('C');
//    List<String> output = Arrays.asList("--A--", "-B-B-", "C---C", "-B-B-", "--A--");
//    assertEquals(result, output);
//  }
}

```

This was the code:
```
package com.codurance.diamond;

import java.util.ArrayList;
import java.util.List;

public class Diamond {

  private static final int LETTER_A = 'A';

  public List<String> lettersUpTo(char letter) {
    ArrayList<String> result = new ArrayList<String>();
    int difference = (int)letter - LETTER_A;

    ArrayList<Character> alphabet = new ArrayList<Character>();

    for (int i = 0; i <= difference; i++) {
      alphabet.add((char)(LETTER_A + i));
    }

    for (int i = 0; i < alphabet.size(); i++) {
      StringBuilder line = new StringBuilder();
      // dash A dash
      Character currentLetter = alphabet.get(i);
      if (currentLetter.equals('A')){
        line.append(dashes(difference - i) + currentLetter + dashes(difference - i));
      }
      else{
        line.append(dashes(difference - i) + currentLetter + dashes((i * 2) - 1) + currentLetter + dashes(difference - i));
      }
       result.add(line.toString());
    }


    result.add(result.get(0));
    return result;
  }



  private String dashes(int number){
   String dashes = "";
    for (int i = 0; i < number; i++) {
      dashes += "-";
    }
    return dashes;
  }

}
```