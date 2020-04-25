Character copy
https://github.com/codurance/apprenticeship/wiki/Character-copy

Pedro Santos edited this page on 23 Oct 2017 · 2 revisions
The character copier is a simple class that reads characters from a source and copies them to a destination one character at a time.

When the method Copy is called on the copier then it should read characters from the source and copy them to the destination until the source returns a newline (‘\n’). The exercise is to implement the character copier using test doubles for the source and the destination (try using spies (manually written mocks) and mocks written with a mocking framework. Start from these definitions:
```
public class Copier
{
	public Copier(…) {}
	public void Copy() {}
}

public interface ISource
{
	char GetChar();
}

public interface IDestination
{
	void SetChar(char character);
}
```
This code looks quite good for this kata:
https://github.com/SafouraBoyot/CopyCharacter

This is the Copier class:
```
public class Copier {
    private final Source source;
    private final Destination destination;

    public Copier(Source source, Destination destination) {
        this.source = source;
        this.destination = destination;
    }


    public void Copy() {
        while (true) {
            char aChar = source.getChar();
            if (aChar == '\n') {
                break;
            }
            destination.setChar(aChar);
        }
    }
}
```
This is the Destination interface:
```
public interface Destination {
    void setChar(char character);
}
```
This is the Source interface:
```
public interface Source {
    char getChar();
}
```
This is the test for Copier:
```
import org.junit.Before;
import org.junit.Test;

import static org.junit.Assert.assertEquals;

public class CopierShould {
    private DestinationSpy destinationSpy;

    @Before
    public void
    setUp() {
        destinationSpy = new DestinationSpy();
    }

    @Test
    public void
    copy_a_character() {

        Source sourceStub = new SourceStub("a\n");
        Copier copier = new Copier(sourceStub, destinationSpy);

        copier.Copy();

        assertEquals(1, destinationSpy.setCharWasCalled);
        assertEquals('a', destinationSpy.verifyCharacter());


    }

    @Test
    public void
    copy_a_line_of_characters() {
        Source sourceStub = new SourceStub("aaaAB" + "\n");
        Copier copier = new Copier(sourceStub, destinationSpy);

        copier.Copy();

        assertEquals(5, destinationSpy.setCharWasCalled);
        assertEquals("aaaAB", destinationSpy.verifyString());


    }
}
```
This has a test for copy_a_character() and copy_a_line_of_characters(). For these tests this uses a SourceStub:
```
public class SourceStub implements Source {


    private final String input ;
    private int inputCharacterCounter = 0;

    public SourceStub(String input) {
        this.input = input;


    }

    @Override
    public char getChar() {
        return input.charAt(inputCharacterCounter++);
    }
}
```
The Stub stub was just an example class to feed into the constructor of the Copier class. The test then checks if the Destination spy was called. This is the DestinationSpy:
```
public class DestinationSpy implements Destination {
    public int setCharWasCalled = 0;
    private char character;
    private String outPutString="";


    @Override
    public void setChar(char character) {
        this.character = character;
        outPutString=outPutString+character;
        setCharWasCalled ++;
    }

    public char verifyCharacter() {
        return character;
    }

    public String verifyString(){
        return outPutString;
    }
}
```


###### Alexis and Sebastian's tests
This is what Alexis and Sebastian's CopierShould looked like:
```
import static org.junit.jupiter.api.Assertions.assertEquals;

import org.junit.jupiter.api.Test;

public class CopierShould {

    @Test
    public void read_one_character_from_source() {
        KeyboardSpy source = new KeyboardSpy("1");
        IDestination destination = new Console();
        Copier copier = new Copier(source, destination);

        copier.copy();

        assertEquals(1, source.verifyCharactersRead());
    }

    @Test
    public void read_two_characters_from_source() {
        KeyboardSpy source = new KeyboardSpy("22");
        IDestination destination = new Console();
        Copier copier = new Copier(source, destination);

        copier.copy();

        assertEquals(2, source.verifyCharactersRead());
    }

    @Test
    public void read_three_characters_from_source() {
        KeyboardSpy source = new KeyboardSpy("333");
        IDestination destination = new Console();
        Copier copier = new Copier(source, destination);

        copier.copy();

        assertEquals(3, source.verifyCharactersRead());
    }

    @Test
    public void read_characters_until_newLine_from_source() {
        KeyboardSpy source = new KeyboardSpy("Hello sebastian, REmote working HOOO!!\n");
        IDestination destination = new Console();
        Copier copier = new Copier(source, destination);

        copier.copy();

        assertEquals(39, source.verifyCharactersRead());
    }


}
```
This is the first test:
```
@Test
public void read_one_character_from_source() {
    KeyboardSpy source = new KeyboardSpy("1");
    IDestination destination = new Console();
    Copier copier = new Copier(source, destination);

    copier.copy();

    assertEquals(1, source.verifyCharactersRead());
}
```
This checks the verifyCharactersRead() method. This is implemented in the KeyboardSpy Class which implements ISource:
```
public class KeyboardSpy implements ISource {

    public static final char EOF = '\n';
    private int charactersRead = 0;
    private char[] input;

    public KeyboardSpy(String input) {
        this.input = input.toCharArray();
    }

    public char GetChar() {
        if (charactersRead < input.length) {
            return input[charactersRead++];
        }
        return EOF;
    }

    public int verifyCharactersRead() {
        return charactersRead;
    }
}
```
This method returns the number of charactersRead. The field charactersRead is added to each time we use getChar() by this if statement:
```
if (charactersRead < input.length) {
    return input[charactersRead++];
}
```
I think that Alexis used the return EOF in order to make the test for Copier to pass in the simplest possible way.

###### Ruby until loops:
```
unless and until
Ruby's until statement differs from while in that it loops until a true condition is met. For example:

i = 0
until i == 5
   puts i
   i += 1
end
When executed, the above code produces the following output:

0
1
2
3
4
```

##### This was an example of .thenReturn():
```
Iterator i = mock(Iterator.class);
when(i.next()).thenReturn("Java Code Geeks").thenReturn("Mockito");
String result = i.next() + " " + i.next();
System.out.println(result);
```
The first time next() method is called ‘Java Code Geeks’ is returned and when it’s called the second time ‘Mockito’ is returned. So the result is Java Code Geeks Mockito.


#### Full Alexis and Sebastian example:
![Screenshot 2020-03-16 at 10 55 03](https://user-images.githubusercontent.com/27693622/76759246-31acb400-6783-11ea-9a13-764324833f9a.png)

This was the Copier class:
![Screenshot 2020-03-16 at 13 26 03](https://user-images.githubusercontent.com/27693622/76785768-e8be2500-67ad-11ea-9ae8-0ba86cdaabe0.png)

I quite liked this implementation of the for loop which Alexis implemented as this showed that the for loop and the while loop are quite interchangeable. I also liked Jose's implementation of a recursive method which you can see at the top of this picture:
```
public void copy(){
 char c = source.GetChar();
 if (c == '\n'){
  return;
 }
 destination.setChar(c);
 copy();
}
```
This is recursive because it allows us to call setChar up until the point that there is a new line in the character. It shows how a for loop and a recursive function could be said to have things in common.

This what the while loop looked like for this method:
![Screenshot 2020-03-16 at 13 28 34](https://user-images.githubusercontent.com/27693622/76786108-8580c280-67ae-11ea-9409-c9865c0c42c8.png)

this shows that it is quite similar to the recursive method.

This was another part of the solution of Alexis and Sebastian:
![Screenshot 2020-03-16 at 13 18 56](https://user-images.githubusercontent.com/27693622/76786187-afd28000-67ae-11ea-9956-3b37e58f7569.png)

This used a KeyboardSpy which implemented the Source interface and a ConsoleSpy which implemented the Destination interface.
This is the rest of that test which also used the ParameterizedTest and the "New line" expected result was interesting:

![Screenshot 2020-03-16 at 13 19 05](https://user-images.githubusercontent.com/27693622/76786294-e4463c00-67ae-11ea-9428-4af7ff597253.png)

This is the other part of Alexis and Sebastian's implementation:
![Screenshot 2020-03-16 at 13 19 10](https://user-images.githubusercontent.com/27693622/76786395-13f54400-67af-11ea-8212-d77bacc2a53b.png)

This uses a while loop and then calls setChar() and getchar().

##### Stub version of tests for Copier:
```
package com.codurance.character_copy;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;

import java.util.List;

import static org.junit.jupiter.api.Assertions.*;

public class CopierShould {

  private SourceStub sourceStub;
  private DestinationSpy destinationSpy;
  private Copier copier;

  @BeforeEach
  void setUp() {
    sourceStub = new SourceStub();
    destinationSpy = new DestinationSpy();
    copier = new Copier(sourceStub, destinationSpy);
  }

  @Test
  void destination_should_receive_characters() {
    sourceStub.setInput(List.of('\n'));
    copier.copy();
    assertEquals(1, sourceStub.timesCalled());
  }

  @Test
  void forward_character_to_the_destination() {
    char character = 'H';
    sourceStub.setInput(List.of(character, '\n'));
    copier.copy();

    assertEquals(1, destinationSpy.getCharsCalled().size());
    assertTrue(destinationSpy.getCharsCalled().contains(character));
  }

  @Test
  void forward_characters_to_destination_until_it_receives_a_new_line() {
    char character1 = 'H';
    char character2 = 'I';
    char character3 = '\n';
    char character4 = 'K';
    sourceStub.setInput(List.of(character1, character2, character3, character4));

    copier.copy();

    assertEquals(2, destinationSpy.getCharsCalled().size());
    assertTrue(destinationSpy.getCharsCalled().contains(character1));
    assertTrue(destinationSpy.getCharsCalled().contains(character2));
    assertFalse(destinationSpy.getCharsCalled().contains(character3));
    assertFalse(destinationSpy.getCharsCalled().contains(character4));
  }

}
```
This used the destinationSpy to check that the size of the getCharsCalled() method increased:
```
class DestinationSpy implements Destination {
  private List<Character> charsCalled;

  public DestinationSpy() {
    this.charsCalled = new ArrayList<>();
  }

  public void setChar(char character) {
    charsCalled.add(character);
  }

  public List<Character> getCharsCalled() {
    return charsCalled;
  }
}
```
This is what DestinationSpy looked like. It had an array on which to add the characters when the setChar(char character) method was called.

The sourceStub was slightly different:
```
class SourceStub implements Source {
  private List<Character> input;
  private int timesCalled = 0;

  public char getChar() {
    char next = input.get(timesCalled);
    timesCalled++;
    return next;
  }

  public void setInput(List<Character> input) {
    this.input = input;
  }

  public int timesCalled() {
    return timesCalled;
  }
}
```
This Class implemented the Interface Source but called get on the input field which was filled by the setInput method. The getChar on this class returned the next char from input.get(timesCalled). I changed this to input.get(timesCalled++).

The Copier class looks like this:
```
package com.codurance.character_copy;

public class Copier {
  private Destination destination;
  private Source source;

  public Copier(Source source, Destination destination) {
    this.source = source;
    this.destination = destination;
  }

  public void copy() {
    while (true) {
      char character = source.getChar();
      if (character == '\n') {
        break;
      }
      destination.setChar(character);
    }
  }
}
```
We changed the copy() method to include while (true). This uses a while loop to get the character from the source and assign this to character. If there is a character with '\n' we then break the loop.

This was the test with Mockito:
```
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.Mock;

import static org.mockito.Mockito.*;

public class CopierShould {

  @Mock Source source;
  @Mock Destination destination;
  private Copier copier;

  @BeforeEach
  void setUp() {
    copier = new Copier(source, destination);
  }

  @Test
  void destination_should_receive_characters() {
    copier.copy();
    verify(source).getChar();
  }

  @Test
  void forward_character_to_the_destination() {
    char character = 'H';
    when(source.getChar()).thenReturn(character);
    copier.copy();
    verify(destination).setChar(character);
  }

  @Test
  void forward_characters_to_destination_until_it_receives_a_new_line() {
    char character1 = 'H';
    char character2 = 'I';
    char character3 = '\n';
    char character4 = 'K';
    when(source.getChar()).thenReturn(character1)
            .thenReturn(character2)
            .thenReturn(character3)
            .thenReturn(character4);
    for (int i = 0; i < 4; i++) {
      copier.copy();
    }

    verify(destination).setChar(character1);
    verify(destination).setChar(character2);
    verify(destination, never()).setChar(character3);
    verify(destination, never()).setChar(character4);
  }
}
```
Here we use:
```
@Mock Source source;
@Mock Destination destination;
```
We also use when().thenReturn() to specify what we want to return:
```
when(source.getChar()).thenReturn(character);
```
The other point at the top of the class is:
```
@ExtendWith(MockitoExtension.class)
```
I think I may have missed this out.

