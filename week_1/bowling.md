X|X|X|X|X|X|X|X|X|X||XX
9-|9-|9-|9-|9-|9-|9-|9-|9-|9-||


Character.isDigit(ballChar)
Character.getNumericValue(ballChar)

We don't have a full picture of the problem we are trying to solve. Mars Rover is only current place and position. Bowling Kata is not about just one.


This is the code for the bowling Kata I did before:
```
public class Game {
    private int roll = 0;
    private int[] rolls = new int[21];

    public void roll(int...rolls) {
        for (int pinsDown : rolls)
            roll(pinsDown);
    }


    private void roll(int pinsDown){
        rolls[roll++] = pinsDown;
    }

    public int score(){
        int score = 0;
        int cursor = 0;
        for (int frame = 0; frame < 10; frame++){
            if (isStrike(cursor)){
                score += 10 + rolls[cursor+1] + rolls[cursor+2];
                cursor++;
            }
            else if (isSpare(cursor)){
                score += 10 + rolls[cursor+2];
                cursor += 2;
            }else {
                score += rolls[cursor] + rolls[cursor+1];
                cursor += 2;
            }
        }
        return score;
    }

    private boolean isStrike(int cursor) {
        return rolls[cursor] == 10;
    }

    private boolean isSpare(int cursor) {
        return rolls[cursor] + rolls[cursor+1] == 10;
    }
}
```

This is the test:
```
import org.junit.Before;
import org.junit.Test;

import static org.junit.Assert.*;

public class GameTest {
    Game game;

    @Before
    public void setUp(){
        game = new Game();
    }

    @Test
    public void canScoreGutterGame(){
        game.roll(0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0);
        assertEquals(0, game.score());
    }

    @Test
    public void canScoreGameOfOnes(){
        game.roll(1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1);
        assertEquals(20, game.score());
    }

    @Test
    public void canScoreSpareFollowedByThree(){
        game.roll(5, 5, 3, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0);
        assertEquals(16, game.score());
    }

    @Test
    public void canScoreStrikeFollowedByThreeThenThree(){
        game.roll(10, 3,3, 0,0, 0,0, 0,0, 0,0, 0,0, 0,0, 0,0, 0,0);
        assertEquals(22, game.score());
    }

    @Test
    public void canScorePerfectGame(){
        game.roll(10, 10, 10, 10, 10, 10, 10, 10, 10, 10, 10, 10);
        assertEquals(300, game.score());
    }

```

This was useful code for splitting an array into different arrays:
https://e.printstacktrace.blog/divide-a-list-to-lists-of-n-size-in-Java-8/
```
final List<Integer> numbers = Arrays.asList(1,2,3,4,5,6,7);
final int chunkSize = 3;
final AtomicInteger counter = new AtomicInteger();

final Collection<List<Integer>> result = numbers.stream()
    .collect(Collectors.groupingBy(it -> counter.getAndIncrement() / chunkSize))
    .values();

System.out.println(result);
```

This same solution was suggested on stack overflow:
https://stackoverflow.com/questions/12026885/is-there-a-common-java-utility-to-break-a-list-into-batches
```
public static <T> Collection<List<T>> prepareChunks(List<T> inputList, int chunkSize) {
    AtomicInteger counter = new AtomicInteger();
    return inputList.stream().collect(Collectors.groupingBy(it -> counter.getAndIncrement() / chunkSize)).values();
}
```

This is an overview of the Java Character class:
https://www.javatpoint.com/post/java-character


![Screenshot 2020-03-03 at 16 54 30](https://user-images.githubusercontent.com/27693622/75861589-805a6580-5df5-11ea-818f-c2d5ed49ea99.png)

Bowling test:
```
import org.junit.Before;
import org.junit.Test;

import static org.junit.Assert.*;

public class GameTest {
    Game game;

    @Before
    public void setUp(){
        game = new Game();
    }

    @Test
    public void canScoreGutterGame(){
        game.roll(0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0);
        assertEquals(0, game.score());
    }

    @Test
    public void canScoreGameOfOnes(){
        game.roll(1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1);
        assertEquals(20, game.score());
    }

    @Test
    public void canScoreSpareFollowedByThree(){
        game.roll(5, 5, 3, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0);
        assertEquals(16, game.score());
    }

    @Test
    public void canScoreStrikeFollowedByThreeThenThree(){
        game.roll(10, 3,3, 0,0, 0,0, 0,0, 0,0, 0,0, 0,0, 0,0, 0,0);
        assertEquals(22, game.score());
    }

    @Test
    public void canScorePerfectGame(){
        game.roll(10, 10, 10, 10, 10, 10, 10, 10, 10, 10, 10, 10);
        assertEquals(300, game.score());
    }
}
```

This was the code for the game:
```
public class Game {
    private int roll = 0;
    private int[] rolls = new int[21];

    public void roll(int...rolls) {
        for (int pinsDown : rolls)
            roll(pinsDown);
    }


    private void roll(int pinsDown){
        rolls[roll++] = pinsDown;
    }

    public int score(){
        int score = 0;
        int cursor = 0;
        for (int frame = 0; frame < 10; frame++){
            if (isStrike(cursor)){
                score += 10 + rolls[cursor+1] + rolls[cursor+2];
                cursor++;
            }
            else if (isSpare(cursor)){
                score += 10 + rolls[cursor+2];
                cursor += 2;
            }else {
                score += rolls[cursor] + rolls[cursor+1];
                cursor += 2;
            }
        }
        return score;
    }

    private boolean isStrike(int cursor) {
        return rolls[cursor] == 10;
    }

    private boolean isSpare(int cursor) {
        return rolls[cursor] + rolls[cursor+1] == 10;
    }
}
```

This is a useful method for checking if a string contains numbers:
```
str.matches(".*\\d.*");
```
and
```
str.matches("-?\\d+");
```

This was the description of the bowling kata:
https://katalyst.codurance.com/bowling

![Screenshot 2020-03-03 at 15 06 30](https://user-images.githubusercontent.com/27693622/75862295-9c123b80-5df6-11ea-80db-cf484aa92a65.png)

#### This is Arnaud's comment on the Bowling method to make it simpler:
```
You can probably simplify your method by this:
return score
        .chars()
        .filter(Character::isDigit)
        .map(Character::getNumericValue)
        .sum();
```