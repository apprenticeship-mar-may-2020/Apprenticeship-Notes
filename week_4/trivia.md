###### Trivia Kata
https://blog.crafties.fr/2017/04/10/trivia-refactoring/

https://github.com/jbrains/trivia/blob/master/java/src/main/java/com/adaptionsoft/games/uglytrivia/Game.java

Characterization Test: the Golden Master
The prerequisite to any refactor is to build a test harness to be sure that the refactoring will not break anything. In this example, it could seem like a waste of time to test all possible paths with unit tests. Fortunately, the code contains a lot of traces that print what the code does. It is then possible to run the code with a set of inputs, capture the output and save it. This output is called the Golden Master. Every time the code is modified, the same inputs are used to run the code and the output is compared to the golden master. As you may have noticed, this will not ensure that the code is bug-free, it will just make sure that the code always behaves the same.

In Java, the golden master can be implemented using a library called approval:
```
<dependency>
    <groupId>com.github.nikolavp</groupId>
    <artifactId>approval-core</artifactId>
    <version>0.3</version>
</dependency>
```
The associated test is shown below (see commit):
```
@Test
public void should_record_and_verify_golden_master() {
    String result = playGame(1L);

    Approvals.verify(result, Paths.get("src", "main", "resources", "approval", "result.txt")); // 2
}

private String playGame(long seed) {
    ByteArrayOutputStream outputStream = new ByteArrayOutputStream();
    System.setOut(new PrintStream(outputStream)); // 3

    boolean notAWinner;
    Game aGame = new Game();

    aGame.add("Chet");
    aGame.add("Pat");
    aGame.add("Sue");

    Random rand = new Random(seed); // 1

    do {

        aGame.roll(rand.nextInt(5) + 1);

        if (rand.nextInt(9) == 7) {
            notAWinner = aGame.wrongAnswer();
        } else {
            notAWinner = aGame.wasCorrectlyAnswered();
        }

    } while (notAWinner);

    return new String(outputStream.toByteArray());
}
```
The above is a GoldenMaster test which is used to ensure that the Game works.

This is the GameRunner:

```
package com.adaptionsoft.games.trivia.runner;
import java.util.Random;

import com.adaptionsoft.games.uglytrivia.Game;


public class GameRunner {

	private static boolean notAWinner;

	public static void main(String[] args) {
		Game aGame = new Game();
		
		aGame.add("Chet");
		aGame.add("Pat");
		aGame.add("Sue");
		
		Random rand = new Random();
	
		do {
			
			aGame.roll(rand.nextInt(5) + 1);
			
			if (rand.nextInt(9) == 7) {
				notAWinner = aGame.wrongAnswer();
			} else {
				notAWinner = aGame.wasCorrectlyAnswered();
			}
			
			
			
		} while (notAWinner);
		
	}
}
```
This is aGame.roll() and forms the interface.

###### This is Game.java

```
package com.adaptionsoft.games.uglytrivia;

import java.util.ArrayList;
import java.util.LinkedList;

public class Game {
    ArrayList players = new ArrayList();
    int[] places = new int[6];
    int[] purses  = new int[6];
    boolean[] inPenaltyBox  = new boolean[6];
    
    LinkedList popQuestions = new LinkedList();
    LinkedList scienceQuestions = new LinkedList();
    LinkedList sportsQuestions = new LinkedList();
    LinkedList rockQuestions = new LinkedList();
    
    int currentPlayer = 0;
    boolean isGettingOutOfPenaltyBox;
    
    public  Game(){
    	for (int i = 0; i < 50; i++) {
			popQuestions.addLast("Pop Question " + i);
			scienceQuestions.addLast(("Science Question " + i));
			sportsQuestions.addLast(("Sports Question " + i));
			rockQuestions.addLast(createRockQuestion(i));
    	}
    }

	public String createRockQuestion(int index){
		return "Rock Question " + index;
	}
	
	public boolean isPlayable() {
		return (howManyPlayers() >= 2);
	}

	public boolean add(String playerName) {
	    players.add(playerName);
	    places[howManyPlayers()] = 0;
	    purses[howManyPlayers()] = 0;
	    inPenaltyBox[howManyPlayers()] = false;
	    
	    System.out.println(playerName + " was added");
	    System.out.println("They are player number " + players.size());
		return true;
	}
	
	public int howManyPlayers() {
		return players.size();
	}

	public void roll(int roll) {
		System.out.println(players.get(currentPlayer) + " is the current player");
		System.out.println("They have rolled a " + roll);
		
		if (inPenaltyBox[currentPlayer]) {
			if (roll % 2 != 0) {
				isGettingOutOfPenaltyBox = true;
				
				System.out.println(players.get(currentPlayer) + " is getting out of the penalty box");
				places[currentPlayer] = places[currentPlayer] + roll;
				if (places[currentPlayer] > 11) places[currentPlayer] = places[currentPlayer] - 12;
				
				System.out.println(players.get(currentPlayer) 
						+ "'s new location is " 
						+ places[currentPlayer]);
				System.out.println("The category is " + currentCategory());
				askQuestion();
			} else {
				System.out.println(players.get(currentPlayer) + " is not getting out of the penalty box");
				isGettingOutOfPenaltyBox = false;
				}
			
		} else {
		
			places[currentPlayer] = places[currentPlayer] + roll;
			if (places[currentPlayer] > 11) places[currentPlayer] = places[currentPlayer] - 12;
			
			System.out.println(players.get(currentPlayer) 
					+ "'s new location is " 
					+ places[currentPlayer]);
			System.out.println("The category is " + currentCategory());
			askQuestion();
		}
		
	}

	private void askQuestion() {
		if (currentCategory() == "Pop")
			System.out.println(popQuestions.removeFirst());
		if (currentCategory() == "Science")
			System.out.println(scienceQuestions.removeFirst());
		if (currentCategory() == "Sports")
			System.out.println(sportsQuestions.removeFirst());
		if (currentCategory() == "Rock")
			System.out.println(rockQuestions.removeFirst());		
	}
	
	
	private String currentCategory() {
		if (places[currentPlayer] == 0) return "Pop";
		if (places[currentPlayer] == 4) return "Pop";
		if (places[currentPlayer] == 8) return "Pop";
		if (places[currentPlayer] == 1) return "Science";
		if (places[currentPlayer] == 5) return "Science";
		if (places[currentPlayer] == 9) return "Science";
		if (places[currentPlayer] == 2) return "Sports";
		if (places[currentPlayer] == 6) return "Sports";
		if (places[currentPlayer] == 10) return "Sports";
		return "Rock";
	}

	public boolean wasCorrectlyAnswered() {
		if (inPenaltyBox[currentPlayer]){
			if (isGettingOutOfPenaltyBox) {
				System.out.println("Answer was correct!!!!");
				purses[currentPlayer]++;
				System.out.println(players.get(currentPlayer) 
						+ " now has "
						+ purses[currentPlayer]
						+ " Gold Coins.");
				
				boolean winner = didPlayerWin();
				currentPlayer++;
				if (currentPlayer == players.size()) currentPlayer = 0;
				
				return winner;
			} else {
				currentPlayer++;
				if (currentPlayer == players.size()) currentPlayer = 0;
				return true;
			}
		} else {
			System.out.println("Answer was corrent!!!!");
			purses[currentPlayer]++;
			System.out.println(players.get(currentPlayer) 
					+ " now has "
					+ purses[currentPlayer]
					+ " Gold Coins.");
			
			boolean winner = didPlayerWin();
			currentPlayer++;
			if (currentPlayer == players.size()) currentPlayer = 0;
			
			return winner;
		}
	}
	
	public boolean wrongAnswer(){
		System.out.println("Question was incorrectly answered");
		System.out.println(players.get(currentPlayer)+ " was sent to the penalty box");
		inPenaltyBox[currentPlayer] = true;
		
		currentPlayer++;
		if (currentPlayer == players.size()) currentPlayer = 0;
		return true;
	}


	private boolean didPlayerWin() {
		return !(purses[currentPlayer] == 6);
	}
}
```
This is the series of instance variables for the Trivia:
```
ArrayList players = new ArrayList();
int[] places = new int[6];
int[] purses  = new int[6];
boolean[] inPenaltyBox  = new boolean[6];

LinkedList popQuestions = new LinkedList();
LinkedList scienceQuestions = new LinkedList();
LinkedList sportsQuestions = new LinkedList();
LinkedList rockQuestions = new LinkedList();

int currentPlayer = 0;
boolean isGettingOutOfPenaltyBox;
```
Here there is a players arrayList, places array, purses array, inPenaltyBox array, popQuestions array, scienceQuestions linkedList, scienceQuestions linkedList, sportsQuestions LinkedList, sportsQuestions, rockQuestions. This is the constructor for the Game class:
```
    public  Game(){
    	for (int i = 0; i < 50; i++) {
			popQuestions.addLast("Pop Question " + i);
			scienceQuestions.addLast(("Science Question " + i));
			sportsQuestions.addLast(("Sports Question " + i));
			rockQuestions.addLast(createRockQuestion(i));
    	}
    }
```
Here the for loop adds questions to the popQuestions, scienceQuestions, sportsQuestions and rockQuestions arrays. These methods are createRockQuestion, isPlayable() and add(): 
```
	public String createRockQuestion(int index){
		return "Rock Question " + index;
	}
	
	public boolean isPlayable() {
		return (howManyPlayers() >= 2);
	}

	public boolean add(String playerName) {
		
		
	    players.add(playerName);
	    places[howManyPlayers()] = 0;
	    purses[howManyPlayers()] = 0;
	    inPenaltyBox[howManyPlayers()] = false;
	    
	    System.out.println(playerName + " was added");
	    System.out.println("They are player number " + players.size());
		return true;
	}
	
```
The isPlayable() method checks that howManyPlayers() is greater than or equal to 2. The createRockQuestion returns a rockQuestion + the index, the add boolean method adds to the players ArrayList sets places[howManyPlayers()] to zero and the purses[howManyPlayers()] to zero. It also sets inPenaltyBox[howManyPlayers()] to false. Next it prints out playerName was added and they are player number players.size(). There is a bit of duplication here because the howManyPlayers() method also returns players.size(). Next we have the roll(int roll) method:
```
public void roll(int roll) {
		System.out.println(players.get(currentPlayer) + " is the current player");
		System.out.println("They have rolled a " + roll);
		
		if (inPenaltyBox[currentPlayer]) {
			if (roll % 2 != 0) {
				isGettingOutOfPenaltyBox = true;
				
				System.out.println(players.get(currentPlayer) + " is getting out of the penalty box");
				places[currentPlayer] = places[currentPlayer] + roll;
				if (places[currentPlayer] > 11) places[currentPlayer] = places[currentPlayer] - 12;
				
				System.out.println(players.get(currentPlayer) 
						+ "'s new location is " 
						+ places[currentPlayer]);
				System.out.println("The category is " + currentCategory());
				askQuestion();
			} else {
				System.out.println(players.get(currentPlayer) + " is not getting out of the penalty box");
				isGettingOutOfPenaltyBox = false;
				}
			
		} else {
		
			places[currentPlayer] = places[currentPlayer] + roll;
			if (places[currentPlayer] > 11) places[currentPlayer] = places[currentPlayer] - 12;
			
			System.out.println(players.get(currentPlayer) 
					+ "'s new location is " 
					+ places[currentPlayer]);
			System.out.println("The category is " + currentCategory());
			askQuestion();
		}
		
	}

```
First this prints out the following:
```
		System.out.println(players.get(currentPlayer) + " is the current player");
		System.out.println("They have rolled a " + roll);
```
This prints the current player and the player's roll. Next we have an if statement:
```

		if (inPenaltyBox[currentPlayer]) {
			if (roll % 2 != 0) {
				isGettingOutOfPenaltyBox = true;
				
				System.out.println(players.get(currentPlayer) + " is getting out of the penalty box");
				places[currentPlayer] = places[currentPlayer] + roll;
				if (places[currentPlayer] > 11) places[currentPlayer] = places[currentPlayer] - 12;
				
				System.out.println(players.get(currentPlayer) 
						+ "'s new location is " 
						+ places[currentPlayer]);
				System.out.println("The category is " + currentCategory());
				askQuestion();
			} else {
				System.out.println(players.get(currentPlayer) + " is not getting out of the penalty box");
				isGettingOutOfPenaltyBox = false;
				}
			
		}
```
This checks if the currentPlayer number is true in the inPenaltyBox array. It then checks if the roll is divisible by 2. It then sets the isGettingOutOfPenaltyBox boolean to true:
```
isGettingOutOfPenaltyBox = true;
```
This then prints the message:
```
System.out.println(players.get(currentPlayer) + " is getting out of the penalty box");
```
to say the current player from the players arrayList is getting out of the penalty box. The method then increments the places array entry for the currentPlayer:
```
places[currentPlayer] = places[currentPlayer] + roll;
```
If the current roll is greater than 11 it then corrects this by setting it back in a similar way to modulo:
```
if (places[currentPlayer] > 11) places[currentPlayer] = places[currentPlayer] - 12;
```
We then print out the player's current position:
```
System.out.println(players.get(currentPlayer) + "'s new location is " + places[currentPlayer]);
```
The category is:
```
System.out.println("The category is " + currentCategory());
```
Finally we call the askQuestion() method:
```
askQuestion();
```
The askQuestion() method does the following:
```
	private void askQuestion() {
		if (currentCategory() == "Pop")
			System.out.println(popQuestions.removeFirst());
		if (currentCategory() == "Science")
			System.out.println(scienceQuestions.removeFirst());
		if (currentCategory() == "Sports")
			System.out.println(sportsQuestions.removeFirst());
		if (currentCategory() == "Rock")
			System.out.println(rockQuestions.removeFirst());		
	}
```
The askQuestion() method checks if the currentCategory() is "Pop", "Science", "Sports" or "Rock" and then calls removeFirst() on each of those objects.

The else statement (if the player has not rolled even):
```
			} else {
				System.out.println(players.get(currentPlayer) + " is not getting out of the penalty box");
				isGettingOutOfPenaltyBox = false;
				}
```
This is the else (if the player is not in the penaltyBox):
```
		} else {
		
			places[currentPlayer] = places[currentPlayer] + roll;
			if (places[currentPlayer] > 11) places[currentPlayer] = places[currentPlayer] - 12;
			
			System.out.println(players.get(currentPlayer) 
					+ "'s new location is " 
					+ places[currentPlayer]);
			System.out.println("The category is " + currentCategory());
			askQuestion();
		}
```
This tells the category and then calls askQuestion:
```
private void askQuestion() {
		if (currentCategory() == "Pop")
			System.out.println(popQuestions.removeFirst());
		if (currentCategory() == "Science")
			System.out.println(scienceQuestions.removeFirst());
		if (currentCategory() == "Sports")
			System.out.println(sportsQuestions.removeFirst());
		if (currentCategory() == "Rock")
			System.out.println(rockQuestions.removeFirst());		
}
```

This is the GameRunner:
```
package com.adaptionsoft.games.trivia.runner;
import java.util.Random;

import com.adaptionsoft.games.uglytrivia.Game;


public class GameRunner {

	private static boolean notAWinner;

	public static void main(String[] args) {
		Game aGame = new Game();
		
		aGame.add("Chet");
		aGame.add("Pat");
		aGame.add("Sue");
		
		Random rand = new Random();
	
		do {
			
			aGame.roll(rand.nextInt(5) + 1);
			
			if (rand.nextInt(9) == 7) {
				notAWinner = aGame.wrongAnswer();
			} else {
				notAWinner = aGame.wasCorrectlyAnswered();
			}
			
			
			
		} while (notAWinner);
		
	}
}
```
This adds Chet, Pat and Sue to the aGame then makes a Random() object. It then calls roll on the game with a number between 0 and 5  + 1 and if the rand.nextInt(9) is equal to 7 the game carries on but if it is not equal to 7 then the game was correctlyAnswered() method is called. If the current player is not in the penalty box then they win.

##### This is the JBrains website:
https://online-training.jbrains.ca/p/surviving-legacy-code

Signed up here:
https://blog.adrianbolboaca.ro/2014/04/from-nothing-to-system-tests/

This is video 1 of the video answer for this challenge:
https://www.youtube.com/watch?v=iPuFiuZ9N2A

This is J.B. Rainsberger's github:
https://github.com/jbrains

I have got up to 1:41:49 of Surviving Legacy Code by JB Rainsberger:
https://online-training.jbrains.ca/courses/36224/lectures/598367

This is the the mentoring program that I would like to sign up for:
https://online-training.jbrains.ca/p/the-jbrains-experience

This is the legacy code retreat github:
https://github.com/celinegilet/Trivia-Kata

##### This is how to remove files from git:
https://www.google.com/search?q=remove+files+from+git&rlz=1C5CHFA_enGB834GB834&oq=remove+files+from+git&aqs=chrome..69i57j0l7.6318j0j7&sourceid=chrome&ie=UTF-8
```
First,Remove files from local repository. git rm -r File-Name. or, remove files only from local repository but from filesystem. ...
Secondly, Commit changes into local repository. git commit -m "unwanted files or some inline comments"
Finally, update/push local changes into remote repository. git push.
```

This is .gitignore.io with Maven and intellij:
http://gitignore.io/api/maven,intellij

```

# Created by https://www.gitignore.io/api/maven,intellij
# Edit at https://www.gitignore.io/?templates=maven,intellij

### Intellij ###
# Covers JetBrains IDEs: IntelliJ, RubyMine, PhpStorm, AppCode, PyCharm, CLion, Android Studio and WebStorm
# Reference: https://intellij-support.jetbrains.com/hc/en-us/articles/206544839

# User-specific stuff
.idea/**/workspace.xml
.idea/**/tasks.xml
.idea/**/usage.statistics.xml
.idea/**/dictionaries
.idea/**/shelf

# Generated files
.idea/**/contentModel.xml

# Sensitive or high-churn files
.idea/**/dataSources/
.idea/**/dataSources.ids
.idea/**/dataSources.local.xml
.idea/**/sqlDataSources.xml
.idea/**/dynamic.xml
.idea/**/uiDesigner.xml
.idea/**/dbnavigator.xml

# Gradle
.idea/**/gradle.xml
.idea/**/libraries

# Gradle and Maven with auto-import
# When using Gradle or Maven with auto-import, you should exclude module files,
# since they will be recreated, and may cause churn.  Uncomment if using
# auto-import.
# .idea/modules.xml
# .idea/*.iml
# .idea/modules
# *.iml
# *.ipr

# CMake
cmake-build-*/

# Mongo Explorer plugin
.idea/**/mongoSettings.xml

# File-based project format
*.iws

# IntelliJ
out/

# mpeltonen/sbt-idea plugin
.idea_modules/

# JIRA plugin
atlassian-ide-plugin.xml

# Cursive Clojure plugin
.idea/replstate.xml

# Crashlytics plugin (for Android Studio and IntelliJ)
com_crashlytics_export_strings.xml
crashlytics.properties
crashlytics-build.properties
fabric.properties

# Editor-based Rest Client
.idea/httpRequests

# Android studio 3.1+ serialized cache file
.idea/caches/build_file_checksums.ser

### Intellij Patch ###
# Comment Reason: https://github.com/joeblau/gitignore.io/issues/186#issuecomment-215987721

# *.iml
# modules.xml
# .idea/misc.xml
# *.ipr

# Sonarlint plugin
.idea/**/sonarlint/

# SonarQube Plugin
.idea/**/sonarIssues.xml

# Markdown Navigator plugin
.idea/**/markdown-navigator.xml
.idea/**/markdown-navigator/

### Maven ###
target/
pom.xml.tag
pom.xml.releaseBackup
pom.xml.versionsBackup
pom.xml.next
release.properties
dependency-reduced-pom.xml
buildNumber.properties
.mvn/timing.properties
.mvn/wrapper/maven-wrapper.jar
.flattened-pom.xml

# End of https://www.gitignore.io/api/maven,intellij
```

This was my trivia practice:
https://github.com/TomSpencerLondon/Trivia-Practice

##### This is a cute kittens site:
http://www.tinykittens.com/

This is Martin Fowler on different ways to approach refactoring:
https://www.infoq.com/presentations/workflow-refactoring/

This was a video from JBrainsberger on surviving legacy code:
https://www.youtube.com/watch?v=d9ktOSUH63w

This was Junit book from JB Rainsberger:
http://testa.roberta.free.fr/My%20Books/Computer%20programming/Java/Manning%20-%20JUnit%20Recipes%20-%20Practical%20Methods%20for%20Programmer%20Testing.pdf
 on JUnit

 This is how far I have got on the course:
 https://online-training.jbrains.ca/courses/36224/lectures/598367

 This is the JBrains experience:
 https://sales.jbrains.ca/the-jbrains-experience/

 This was a fun free video from Adrian Bolboaca from http://mozaicworks.com using the legacy code retreat code ideas:
 https://www.youtube.com/watch?v=ruUb33dhkK4

 This was the second video from that series:
 https://www.youtube.com/watch?v=V4d6vJuDNQM

 This is JBrainsberger's blog:
 https://blog.thecodewhisperer.com/permalink/relative-include-paths-and-the-slow-certain-march-towards-legacy-code


 This is the test so far:
 ```
package com.adaptionsoft.games.trivia;

import com.adaptionsoft.games.uglytrivia.Game;
import org.junit.Before;
import org.junit.Test;

import java.io.ByteArrayOutputStream;
import java.io.IOException;
import java.io.PrintStream;

import static org.junit.Assert.assertEquals;
import static org.junit.Assert.assertTrue;

public class GameTest {
  private Game game;

  @Before
  public void setUp() throws Exception {
    game = new Game();
  }

  @Test
  public void when_game_is_created_nothing_is_written_to_the_output() {
    ByteArrayOutputStream stream = new ByteArrayOutputStream();
    PrintStream printStream = new PrintStream(stream);
    System.setOut(printStream);
    String emptyString = "";

    assertEquals(emptyString, stream.toString());

  }

  @Test
  public void when_two_players_are_added_their_name_and_numbers_are_returned() {
    ByteArrayOutputStream stream = getConsoleOutput();
    String playerNameAndNumber = "Adi was added\n" +
            "They are player number 1\n" +
            "Harry was added\n" +
            "They are player number 2\n";

    String playerName = "Adi";
    String secondPlayerName = "Harry";
    game.add(playerName);
    game.add(secondPlayerName);

    assertEquals(playerNameAndNumber, stream.toString());

  }

  @Test
  public void when_rolling_dice_location_changes_and_question_is_set() {
    ByteArrayOutputStream stream = getConsoleOutput();
    game.add("Adi");
    game.roll(1);

    String result = "Adi was added\n" +
            "They are player number 1\n" +
            "Adi is the current player\n" +
            "They have rolled a 1\n" +
            "Adi's new location is 1\n" +
            "The category is Science\n" +
            "Science Question 0\n";

    assertEquals(result, stream.toString());
  }

  @Test
  public void when_three_players_play_game_works() {
    ByteArrayOutputStream stream = getConsoleOutput();
    game.add("Chet");
    game.add("Pat");
    game.add("Sue");
    game.roll(3);
    game.wasCorrectlyAnswered();
    game.roll(1);
    game.wasCorrectlyAnswered();
    game.roll(1);
    game.wasCorrectlyAnswered();
    game.roll(3);
    game.wasCorrectlyAnswered();
    game.roll(4);
    game.wasCorrectlyAnswered();
    game.roll(4);
    game.wasCorrectlyAnswered();
    game.roll(3);
    game.wasCorrectlyAnswered();
    game.roll(3);
    game.wasCorrectlyAnswered();
    game.roll(4);
    game.wrongAnswer();
    game.roll(3);
    game.wasCorrectlyAnswered();
    game.roll(2);
    game.wasCorrectlyAnswered();
    game.roll(3);
    game.wasCorrectlyAnswered();
    game.roll(1);
    game.wasCorrectlyAnswered();
    game.roll(4);
    game.wasCorrectlyAnswered();
    game.roll(4);
    game.wasCorrectlyAnswered();
    game.roll(5);
    game.wasCorrectlyAnswered();

    String result = "Chet was added\n" +
            "They are player number 1\n" +
            "Pat was added\n" +
            "They are player number 2\n" +
            "Sue was added\n" +
            "They are player number 3\n" +
            "Chet is the current player\n" +
            "They have rolled a 3\n" +
            "Chet's new location is 3\n" +
            "The category is Rock\n" +
            "Rock Question 0\n" +
            "Answer was corrent!!!!\n" +
            "Chet now has 1 Gold Coins.\n" +
            "Pat is the current player\n" +
            "They have rolled a 1\n" +
            "Pat's new location is 1\n" +
            "The category is Science\n" +
            "Science Question 0\n" +
            "Answer was corrent!!!!\n" +
            "Pat now has 1 Gold Coins.\n" +
            "Sue is the current player\n" +
            "They have rolled a 1\n" +
            "Sue's new location is 1\n" +
            "The category is Science\n" +
            "Science Question 1\n" +
            "Answer was corrent!!!!\n" +
            "Sue now has 1 Gold Coins.\n" +
            "Chet is the current player\n" +
            "They have rolled a 3\n" +
            "Chet's new location is 6\n" +
            "The category is Sports\n" +
            "Sports Question 0\n" +
            "Answer was corrent!!!!\n" +
            "Chet now has 2 Gold Coins.\n" +
            "Pat is the current player\n" +
            "They have rolled a 4\n" +
            "Pat's new location is 5\n" +
            "The category is Science\n" +
            "Science Question 2\n" +
            "Answer was corrent!!!!\n" +
            "Pat now has 2 Gold Coins.\n" +
            "Sue is the current player\n" +
            "They have rolled a 4\n" +
            "Sue's new location is 5\n" +
            "The category is Science\n" +
            "Science Question 3\n" +
            "Answer was corrent!!!!\n" +
            "Sue now has 2 Gold Coins.\n" +
            "Chet is the current player\n" +
            "They have rolled a 3\n" +
            "Chet's new location is 9\n" +
            "The category is Science\n" +
            "Science Question 4\n" +
            "Answer was corrent!!!!\n" +
            "Chet now has 3 Gold Coins.\n" +
            "Pat is the current player\n" +
            "They have rolled a 3\n" +
            "Pat's new location is 8\n" +
            "The category is Pop\n" +
            "Pop Question 0\n" +
            "Answer was corrent!!!!\n" +
            "Pat now has 3 Gold Coins.\n" +
            "Sue is the current player\n" +
            "They have rolled a 4\n" +
            "Sue's new location is 9\n" +
            "The category is Science\n" +
            "Science Question 5\n" +
            "Question was incorrectly answered\n" +
            "Sue was sent to the penalty box\n" +
            "Chet is the current player\n" +
            "They have rolled a 3\n" +
            "Chet's new location is 0\n" +
            "The category is Pop\n" +
            "Pop Question 1\n" +
            "Answer was corrent!!!!\n" +
            "Chet now has 4 Gold Coins.\n" +
            "Pat is the current player\n" +
            "They have rolled a 2\n" +
            "Pat's new location is 10\n" +
            "The category is Sports\n" +
            "Sports Question 1\n" +
            "Answer was corrent!!!!\n" +
            "Pat now has 4 Gold Coins.\n" +
            "Sue is the current player\n" +
            "They have rolled a 3\n" +
            "Sue is getting out of the penalty box\n" +
            "Sue's new location is 0\n" +
            "The category is Pop\n" +
            "Pop Question 2\n" +
            "Answer was correct!!!!\n" +
                    "Sue now has 3 Gold Coins.\n" +
            "Chet is the current player\n" +
            "They have rolled a 1\n" +
            "Chet's new location is 1\n" +
            "The category is Science\n" +
            "Science Question 6\n" +
            "Answer was corrent!!!!\n" +
            "Chet now has 5 Gold Coins.\n" +
            "Pat is the current player\n" +
            "They have rolled a 4\n" +
            "Pat's new location is 2\n" +
            "The category is Sports\n" +
            "Sports Question 2\n" +
            "Answer was corrent!!!!\n" +
            "Pat now has 5 Gold Coins.\n" +
            "Sue is the current player\n" +
            "They have rolled a 4\n" +
            "Sue is not getting out of the penalty box\n" +
            "Chet is the current player\n" +
            "They have rolled a 5\n" +
            "Chet's new location is 6\n" +
            "The category is Sports\n" +
            "Sports Question 3\n" +
            "Answer was corrent!!!!\n" +
            "Chet now has 6 Gold Coins.\n";

    assertEquals(result, stream.toString());
  }

  @Test
  public void true_is_true() throws IOException {
    GoldenMaster goldenMaster = new GoldenMaster();
    goldenMaster.generateGoldenMaster();
    assertTrue(true);
  }

  private ByteArrayOutputStream getConsoleOutput() {
    ByteArrayOutputStream stream = new ByteArrayOutputStream();
    PrintStream printStream = new PrintStream(stream);
    System.setOut(printStream);
    return stream;
  }
}
```

This is the GoldenMaster test:
```
package com.adaptionsoft.games.trivia;

import com.adaptionsoft.games.trivia.runner.GameRunner;

import java.io.*;
import java.util.Random;

public class GoldenMaster {
  public String getGameResult(long seed){
    ByteArrayOutputStream stream = new ByteArrayOutputStream();
    PrintStream printStream = new PrintStream(stream);
    System.setOut(printStream);
    GameRunner.play(new Random(seed));

    return stream.toString();
  }

  public void generateGoldenMaster() throws IOException {
    for (long seed = 0; seed < 1000; seed++) {
      File file = new File(System.getProperty("user.dir") + "/goldenMasterData/" + seed + ".txt");
      FileWriter myWriter = new FileWriter(file);
      myWriter.write(getGameResult(seed));
      myWriter.close();
    }
  }
}
```
This is Jbrains' video on Trivia:
https://online-training.jbrains.ca/courses/36224/lectures/598367


###### This was our work on the Trivia Kata (Sebastian + Me):
This was our GoldenMaster test:
```
package com.adaptionsoft.games.trivia;

import com.adaptionsoft.games.trivia.runner.GameRunner;
import org.junit.Test;

import java.io.File;
import java.io.FileNotFoundException;
import java.util.Scanner;

import static org.junit.Assert.assertEquals;

public class GoldenMasterTest {

	@Test
	public void golden_master_test() throws Exception {
		String golden = getStringBuilder("src/goldenMaster.txt").toString();

		GameRunnerTest.main(new String[]{});

		String sample = getStringBuilder("src/sample.txt").toString();
		assertEquals(golden, sample);
	}

	private StringBuilder getStringBuilder(String path) throws FileNotFoundException {
		StringBuilder file = new StringBuilder();
		File goldenMaster = new File(path);
		Scanner myReader = new Scanner(goldenMaster);
		while (myReader.hasNextLine()) {
			file.append(myReader.nextLine());

		}
		System.out.println(file.toString());
		myReader.close();
		return file;
	}
}
```
This was the Stringbuilder() method:
```
	private StringBuilder getStringBuilder(String path) throws FileNotFoundException {
		StringBuilder file = new StringBuilder();
		File goldenMaster = new File(path);
		Scanner myReader = new Scanner(goldenMaster);
		while (myReader.hasNextLine()) {
			file.append(myReader.nextLine());

		}
		System.out.println(file.toString());
		myReader.close();
		return file;
	}
```
This creates a StringBuilder and assigns it to the file Stringbuilder variable. We then create a new File() from the path and then a Scanner with the file variable wrapped inside. Next we use a while loop to append to the Stringbuilder() file variable while the myReader Scanner variable hasNextLine(). Next we System.out.println(file.toString()). We didn't need to do this. Next we use myReader.close() to close the Scanner and then we return the file from the StringBuilder() method. The test on the other hand takes an already copy and pasted GoldenMaster.txt file and compares these according to the StringBuilder() result:
```

	@Test
	public void golden_master_test() throws Exception {
		String golden = getStringBuilder("src/goldenMaster.txt").toString();

		GameRunnerTest.main(new String[]{});

		String sample = getStringBuilder("src/sample.txt").toString();
		assertEquals(golden, sample);
	}
```
We made this sample.txt in the main method of our GameRunnerTest class:
```

public class GameRunnerTest {

  private static boolean notAWinner;

  public static void main(String[] args) throws FileNotFoundException {

    File file = new File("src/sample.txt");
    PrintStream stream = new PrintStream(file);
    System.setOut(stream);

    Game aGame = new Game();

    aGame.add("Chet");
    aGame.add("Pat");
    aGame.add("Sue");

    Random rand = new Random(3);

    do {

      aGame.roll(rand.nextInt(5) + 1);

      if (rand.nextInt(9) == 7) {
        notAWinner = aGame.wrongAnswer();
      } else {
        notAWinner = aGame.wasCorrectlyAnswered();
      }



    } while (notAWinner);

  }
}

```
The main piece of code that we added to our GameRunnerTest main method was:
```

    File file = new File("src/sample.txt");
    PrintStream stream = new PrintStream(file);
    System.setOut(stream);

```
This creates a file object from the src/sample.txt file and then creates a PrintStream from the file with PrintStream(file). We then use System.setOut(stream) to ensure that any output from System.out.println() is outputed to our file. After some refactoring our Game class looked like this:
```
package com.adaptionsoft.games.uglytrivia;

import java.util.ArrayList;

public class Game {
    private final Questions questions;
    private final Messenger messenger;

    ArrayList<Player> playersList = new ArrayList<Player>();

    int currentPlayer = 0;
    boolean isGettingOutOfPenaltyBox;

    public Game() {
        this.questions = new Questions();
        this.messenger = new Messenger();
    }

    public boolean add(String playerName) {
        playersList.add(new Player(playerName));
        messenger.playerAdded(playerName, playersList.size());
        return true;
    }

    public void roll(int roll) {

        messenger.roll(roll, currentPlayer().name);

        if (currentPlayer().isInPenaltyBox()) {
            inPenaltyBox(roll);
            return;
        }
        playerTurn(roll);
    }

    public boolean wrongAnswer() {
        messenger.incorrectAnswer(currentPlayer().name);
        currentPlayer().setInPenaltyBox(true);
        changeToNextPlayer();
        return true;
    }

    public boolean wasCorrectlyAnswered() {

        if (currentPlayer().isInPenaltyBox()) {
            return gettingFromPenalty();
        }
        return correctAnswer("Answer was corrent!!!!");
    }

    private boolean correctAnswer(String message) {
        currentPlayer().addToPurse();
        messenger.correctAnswer(message, currentPlayer().name, currentPlayer().getPurse());
        boolean winner = didPlayerWin();
        changeToNextPlayer();
        return winner;
    }

    private void inPenaltyBox(int roll) {
        if (roll % 2 != 0) {
            isGettingOutOfPenaltyBox = true;
            messenger.playerOutOfPenalty(currentPlayer().name);
            playerTurn(roll);
        } else {
            messenger.notGettingOutOfPenalty(currentPlayer().name);
            isGettingOutOfPenaltyBox = false;
        }
    }

    protected boolean gettingFromPenalty() {
        if (isGettingOutOfPenaltyBox) {
            return correctAnswer("Answer was correct!!!!");
        }
        changeToNextPlayer();
        return true;
    }

    private void playerTurn(int roll) {
        movePlayer(roll);
        messenger.printCategory(questions.currentCategory(currentPlayer().getPlace()));
        questions.askQuestion();
    }

    private void movePlayer(int roll) {
        currentPlayer().move(roll);
        messenger.moveMessage(currentPlayer().name, currentPlayer().getPlace());
    }

    private Player currentPlayer() {
        return playersList.get(currentPlayer);
    }

    private void changeToNextPlayer() {
        currentPlayer++;
        if (currentPlayer == playersList.size()) {
            currentPlayer = 0;
        }
    }

    private boolean didPlayerWin() {
        return !(currentPlayer().getPurse() == 6);
    }
}

```
Here we edit the Game to include the following fields:
```
    private final Questions questions;
    private final Messenger messenger;

    ArrayList<Player> playersList = new ArrayList<Player>();

    int currentPlayer = 0;
    boolean isGettingOutOfPenaltyBox;

    public Game() {
        this.questions = new Questions();
        this.messenger = new Messenger();
    }
```
This includes the Questions and Messenger classes and ArrayList which will be filled with Players. We also have a currentPlayer integer and a boolean isGettingOutOfPenaltyBox which are both variables which would ideally be edited in future versions. One option is to extract the isGettingOutOfPenaltyBox to the Player class. We would ideally like to extract the currentPlayer integer variable to a Players class which would then return the nextPlayer. The add() and roll(int roll) methods are interesting:
```
    public boolean add(String playerName) {
        playersList.add(new Player(playerName));
        messenger.playerAdded(playerName, playersList.size());
        return true;
    }

    public void roll(int roll) {

        messenger.roll(roll, currentPlayer().name);

        if (currentPlayer().isInPenaltyBox()) {
            inPenaltyBox(roll);
            return;
        }
        playerTurn(roll);
    }
```
These are both methods that have been refactored. The add method add the new Player(playerName) to the ArrayList playersList and then sends the playerAdded method to the messenger class so that it then prints the message for adding the player. The roll method sends the roll method to the messenger and then if the currentPlayer() isInPenaltyBox it then calls inPenaltyBox(roll) and returns otherwise it calls playerTurn(roll). The wrongAnswer() method and the wasCorrectlyAnswered() methods look like this:
```
public boolean wrongAnswer() {
    messenger.incorrectAnswer(currentPlayer().name);
    currentPlayer().setInPenaltyBox(true);
    changeToNextPlayer();
    return true;
}

public boolean wasCorrectlyAnswered() {

    if (currentPlayer().isInPenaltyBox()) {
        return gettingFromPenalty();
    }
    return correctAnswer("Answer was corrent!!!!");
}
```
The wrongAnswer method calls the incorrectAnswer on messenger and sets the currentPlayer() to the penaltyBox and sets the turn to the next player. The wasCorrectlyAnswered() method checks if the currentPlayer() is in the penaltyBox if so it sets a boolean for gettingFromPenalty with gettingFromPenalty() otherwise it calls correctAnswer. The next two methods:
```

    private boolean correctAnswer(String message) {
        currentPlayer().addToPurse();
        messenger.correctAnswer(message, currentPlayer().name, currentPlayer().getPurse());
        boolean winner = didPlayerWin();
        changeToNextPlayer();
        return winner;
    }

    private void inPenaltyBox(int roll) {
        if (roll % 2 != 0) {
            isGettingOutOfPenaltyBox = true;
            messenger.playerOutOfPenalty(currentPlayer().name);
            playerTurn(roll);
        } else {
            messenger.notGettingOutOfPenalty(currentPlayer().name);
            isGettingOutOfPenaltyBox = false;
        }
    }
```
The correctAnswer adds to the purse sends correctAnswer to the messenger and then reassigns a boolean on winner with didPlayerWin(). The rest of the methods:
```

protected boolean gettingFromPenalty() {
    if (isGettingOutOfPenaltyBox) {
        return correctAnswer("Answer was correct!!!!");
    }
    changeToNextPlayer();
    return true;
}

private void playerTurn(int roll) {
    movePlayer(roll);
    messenger.printCategory(questions.currentCategory(currentPlayer().getPlace()));
    questions.askQuestion();
}

private void movePlayer(int roll) {
    currentPlayer().move(roll);
    messenger.moveMessage(currentPlayer().name, currentPlayer().getPlace());
}

private Player currentPlayer() {
    return playersList.get(currentPlayer);
}

private void changeToNextPlayer() {
    currentPlayer++;
    if (currentPlayer == playersList.size()) {
        currentPlayer = 0;
    }
}

private boolean didPlayerWin() {
    return !(currentPlayer().getPurse() == 6);
}
```
The gettingFromPenalty() method and playerTurn() methods:
```
protected boolean gettingFromPenalty() {
    if (isGettingOutOfPenaltyBox) {
        return correctAnswer("Answer was correct!!!!");
    }
    changeToNextPlayer();
    return true;
}

private void playerTurn(int roll) {
    movePlayer(roll);
    messenger.printCategory(questions.currentCategory(currentPlayer().getPlace()));
    questions.askQuestion();
}
```
These return a boolean for getting from the PenaltyBox and print for the printCategory on the messenger. These next methods are interesting:
```

    private void movePlayer(int roll) {
        currentPlayer().move(roll);
        messenger.moveMessage(currentPlayer().name, currentPlayer().getPlace());
    }

    private Player currentPlayer() {
        return playersList.get(currentPlayer);
    }

    private void changeToNextPlayer() {
        currentPlayer++;
        if (currentPlayer == playersList.size()) {
            currentPlayer = 0;
        }
    }

    private boolean didPlayerWin() {
        return !(currentPlayer().getPurse() == 6);
    }
```
The movePlayer() method calls move on the currentPlayer and then returns calls the messenger for this. The currentPlayer() gets the currentPlayer from the playersList ArrayLsit. The changeToNextPlayer adds to the currentPlayer++ or sets this as zero if the currentPlayer is equal to the length of the playersList array. The boolean didPlayerWin does the opposite of what the name suggests. It returns the opposite of currentPlayer().getPurse is equal to 6 which is way in which a player would win. Some of the methods from the Game class have been extracted to the Messenger class:
```
package com.adaptionsoft.games.uglytrivia;

public class Messenger {
    public void playerAdded(String playerName, int playerNumber) {
        System.out.println(playerName + " was added");
        System.out.println("They are player number " + playerNumber);
    }

    public void incorrectAnswer(String name) {
        System.out.println("Question was incorrectly answered");
        System.out.println(name + " was sent to the penalty box");
    }

    public void correctAnswer(String message, String name, int purse) {
        System.out.println(message);
        System.out.println(name
                + " now has "
                + purse
                + " Gold Coins.");
    }

    public void moveMessage(String name, int place) {
        System.out.println(name
                + "'s new location is "
                + place);
    }

    public void roll(int roll, String name) {
        System.out.println(name + " is the current player");
        System.out.println("They have rolled a " + roll);
    }

    public void printCategory(String category) {
        System.out.println("The category is " + category);
    }

    public void playerOutOfPenalty(String name) {
        System.out.println(name + " is getting out of the penalty box");
    }

    public void notGettingOutOfPenalty(String name) {
        System.out.println(name + " is not getting out of the penalty box");
    }
}
```
These are all messages that are printed on each of the corresponding methods in the Game class. The Player class has also taken some of the responsibility from the Game class:
```
package com.adaptionsoft.games.uglytrivia;

public class Player {

  public static final int MAX_POSITION = 12;
  public final String name;
  private int place;
  private boolean inPenaltyBox;
  private int purse;

  public Player(String name) {
    this.purse = 0;
    this.name = name;
    this.inPenaltyBox = false;
    this.place = 0;
  }

  public int getPlace() {
    return place;
  }

  public void move(int roll) {
    this.place += roll;
    this.place %= MAX_POSITION;
  }

  public int getPurse() {
    return purse;
  }

  public void addToPurse() {
    this.purse++;
  }

  public boolean isInPenaltyBox() {
    return inPenaltyBox;
  }

  public void setInPenaltyBox(boolean inPenaltyBox) {
    this.inPenaltyBox = inPenaltyBox;
  }
}

```
The player now holds the state for purse, name, inPenaltyBox boolean and the player's place on the board:
```
  public static final int MAX_POSITION = 12;
  public final String name;
  private int place;
  private boolean inPenaltyBox;
  private int purse;

  public Player(String name) {
    this.purse = 0;
    this.name = name;
    this.inPenaltyBox = false;
    this.place = 0;
  }

```
It also has a series of getters and setters related to these states:
```

  public int getPlace() {
    return place;
  }

  public void move(int roll) {
    this.place += roll;
    this.place %= MAX_POSITION;
  }

  public int getPurse() {
    return purse;
  }

  public void addToPurse() {
    this.purse++;
  }

  public boolean isInPenaltyBox() {
    return inPenaltyBox;
  }

  public void setInPenaltyBox(boolean inPenaltyBox) {
    this.inPenaltyBox = inPenaltyBox;
  }
}
```
Sebastian was very clever to extract the Questions class. This was a kind of Repository for the String Questions in the Game class:
```
package com.adaptionsoft.games.uglytrivia;

import java.util.LinkedList;

public class Questions {
    LinkedList popQuestions = new LinkedList();
    LinkedList scienceQuestions = new LinkedList();
    LinkedList sportsQuestions = new LinkedList();
    LinkedList rockQuestions = new LinkedList();
    private int categoryNumber;

    public Questions() {
        for (int i = 0; i < 50; i++) {
            popQuestions.addLast("Pop Question " + i);
            scienceQuestions.addLast(("Science Question " + i));
            sportsQuestions.addLast(("Sports Question " + i));
            rockQuestions.addLast("Rock Question " + i);
        }
    }

    void askQuestion() {
        if (categoryNumber == 0)
            System.out.println(popQuestions.removeFirst());
        if (categoryNumber == 1)
            System.out.println(scienceQuestions.removeFirst());
        if (categoryNumber == 2)
            System.out.println(sportsQuestions.removeFirst());
        if (categoryNumber == 3)
            System.out.println(rockQuestions.removeFirst());
    }

    String currentCategory(int place) {
        categoryNumber = place % 4;

        if (categoryNumber == 0)
            return "Pop";

        if (categoryNumber == 1)
            return "Science";

        if (categoryNumber == 2)
            return "Sports";

        return "Rock";
    }
}
```
The state on this class includes the following:
```
LinkedList popQuestions = new LinkedList();
LinkedList scienceQuestions = new LinkedList();
LinkedList sportsQuestions = new LinkedList();
LinkedList rockQuestions = new LinkedList();
private int categoryNumber;

public Questions() {
    for (int i = 0; i < 50; i++) {
        popQuestions.addLast("Pop Question " + i);
        scienceQuestions.addLast(("Science Question " + i));
        sportsQuestions.addLast(("Sports Question " + i));
        rockQuestions.addLast("Rock Question " + i);
    }
}
```
These are LinkedLists with popQuestions, scienceQuestions, sportsQuestions, rockQuestions and a categoryNumber integer. We also have the askQuestion and studentCategory methods:
```
    void askQuestion() {
        if (categoryNumber == 0)
            System.out.println(popQuestions.removeFirst());
        if (categoryNumber == 1)
            System.out.println(scienceQuestions.removeFirst());
        if (categoryNumber == 2)
            System.out.println(sportsQuestions.removeFirst());
        if (categoryNumber == 3)
            System.out.println(rockQuestions.removeFirst());
    }

    String currentCategory(int place) {
        categoryNumber = place % 4;

        if (categoryNumber == 0)
            return "Pop";

        if (categoryNumber == 1)
            return "Science";

        if (categoryNumber == 2)
            return "Sports";

        return "Rock";
    }
```
These each return the correct question according to the place of the player on the board.

##### This was Ewan and Alexis' Trivia kata:
https://github.com/ewansheldon/trivia/tree/changing-golden-master/java
