https://github.com/emilybache/Tennis-Refactoring-Kata

This is the Ruby video on the Tennis Kata:
https://www.youtube.com/watch?v=swokhWHKDmc&list=PLr2F5WUnrawteDffEUvCLSzZklOErPIdR
This is 19 videos long... 

This is the description of the Kata:
https://katalyst.codurance.com/tennis

This was the series of tests for the Tennis kata:
```
import static org.junit.Assert.*;

import java.util.Arrays;
import java.util.Collection;

import org.junit.Test;
import org.junit.runner.RunWith;
import org.junit.runners.Parameterized;
import org.junit.runners.Parameterized.Parameters;

@RunWith(Parameterized.class)
public class TennisTest {
    private int player1Score;
    private int player2Score;
    private String expectedScore;

    public TennisTest(int player1Score, int player2Score, String expectedScore) {
        this.player1Score = player1Score;
        this.player2Score = player2Score;
        this.expectedScore = expectedScore;
    }
    
    @Parameters
    public static Collection<Object[]> getAllScores() {
        return Arrays.asList(new Object[][] {
                { 0, 0, "Love-All" },
                { 1, 1, "Fifteen-All" },
                { 2, 2, "Thirty-All"},
                { 3, 3, "Deuce"},
                { 4, 4, "Deuce"},
                
                { 1, 0, "Fifteen-Love"},
                { 0, 1, "Love-Fifteen"},
                { 2, 0, "Thirty-Love"},
                { 0, 2, "Love-Thirty"},
                { 3, 0, "Forty-Love"},
                { 0, 3, "Love-Forty"},
                { 4, 0, "Win for player1"},
                { 0, 4, "Win for player2"},
                
                { 2, 1, "Thirty-Fifteen"},
                { 1, 2, "Fifteen-Thirty"},
                { 3, 1, "Forty-Fifteen"},
                { 1, 3, "Fifteen-Forty"},
                { 4, 1, "Win for player1"},
                { 1, 4, "Win for player2"},

                { 3, 2, "Forty-Thirty"},
                { 2, 3, "Thirty-Forty"},
                { 4, 2, "Win for player1"},
                { 2, 4, "Win for player2"},
                
                { 4, 3, "Advantage player1"},
                { 3, 4, "Advantage player2"},
                { 5, 4, "Advantage player1"},
                { 4, 5, "Advantage player2"},
                { 15, 14, "Advantage player1"},
                { 14, 15, "Advantage player2"},

                { 6, 4, "Win for player1"},
                { 4, 6, "Win for player2"},
                { 16, 14, "Win for player1"},
                { 14, 16, "Win for player2"},
        });
    }

    public void checkAllScores(TennisGame game) {
        int highestScore = Math.max(this.player1Score, this.player2Score);
        for (int i = 0; i < highestScore; i++) {
            if (i < this.player1Score)
                game.wonPoint("player1");
            if (i < this.player2Score)
                game.wonPoint("player2");
        }
        assertEquals(this.expectedScore, game.getScore());
    }

    @Test
    public void checkAllScoresTennisGame1() {
        TennisGame1 game = new TennisGame1();
        checkAllScores(game);
    }

    @Test
    public void checkAllScoresTennisGame2() {
        TennisGame2 game = new TennisGame2("player1", "player2");
        checkAllScores(game);
    }

    @Test
    public void checkAllScoresTennisGame3() {
        TennisGame3 game = new TennisGame3("player1", "player2");
        checkAllScores(game);
    }

}

```
This was the solution to the Kata:
```
import java.util.Map;

public class TennisGame1 implements TennisGame {

  public static final String SEPERATOR = "-";
  private int player1Score = 0;
    private int player2Score = 0;
    private final Map<Integer, String> draw;
    private final Map<Integer, String> score;
    public static final String ADVANTAGE = "Advantage player";
    public static final String WIN = "Win for player";

    public TennisGame1() {
        draw = Map.of(
            0, "Love-All",
            1, "Fifteen-All",
            2, "Thirty-All",
            3, "Deuce",
            4, "Deuce"
        );
        score = Map.of(
            0, "Love",
            1, "Fifteen",
            2, "Thirty",
            3, "Forty"
        );
    }


    public void wonPoint(String playerName) {
        if (playerName.equals("player1")) {
            player1Score++;
        } else {
            player2Score++;
        }
    }

    public String getScore() {
        if (player1Score == player2Score) {
            return draw();
        }

        if (player1Score >= 4 || player2Score >= 4) {
            return winOrWinning();
        }

        return totalScore();
    }

  private String totalScore() {
    return playerScore(player1Score) + SEPERATOR + playerScore(player2Score);
  }

  private String playerScore(int scoreNumber) {
        return score.get(scoreNumber);
    }

    private String winOrWinning() {
        int scoreDiff = player1Score - player2Score;
        if (scoreDiff == 1) {
            return ADVANTAGE + "1";
        } else if (scoreDiff == -1) {
            return ADVANTAGE + "2";
        } else if (scoreDiff >= 2) {
            return WIN + "1";
        }
        return WIN + "2";
    }

    private String draw() {
        return draw.get(player1Score);
    }
}

```
This was the Maps for the Tennis:
```
private final Map<Integer, String> draw;
private final Map<Integer, String> score;

draw = Map.of(
            0, "Love-All",
            1, "Fifteen-All",
            2, "Thirty-All",
            3, "Deuce",
            4, "Deuce"
        );
        score = Map.of(
            0, "Love",
            1, "Fifteen",
            2, "Thirty",
            3, "Forty"
        );
```

This is now the getScore() method:
```
    public String getScore() {
        if (player1Score == player2Score) {
            return draw();
        }

        if (player1Score >= 4 || player2Score >= 4) {
            return winOrWinning();
        }

        return totalScore();
    }
```

This is the totalScore():
```

  private String totalScore() {
    
      return playerScore(player1Score) + SEPERATOR + playerScore(player2Score);
  }
```
This is the playerScore() method:
```
  private String playerScore(int scoreNumber) {
      return score.get(scoreNumber);
    }
```
This is the winOrWinning method:
```
private String winOrWinning() {
        int scoreDiff = player1Score - player2Score;
        if (scoreDiff == 1) {
            return ADVANTAGE + "1";
        } else if (scoreDiff == -1) {
            return ADVANTAGE + "2";
        } else if (scoreDiff >= 2) {
            return WIN + "1";
        }
        return WIN + "2";
    }
```
This is the draw() method:
```

    private String draw() {
      return draw.get(player1Score);
    }
```
This is the test for the Tennis Kata:
```
import static org.junit.Assert.*;

import java.util.Arrays;
import java.util.Collection;

import org.junit.Before;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.junit.runners.Parameterized;
import org.junit.runners.Parameterized.Parameters;

@RunWith(Parameterized.class)
public class TennisTest {

    private int player1Score;
    private int player2Score;
    private String expectedScore;

    public TennisTest(int player1Score, int player2Score, String expectedScore) {
        this.player1Score = player1Score;
        this.player2Score = player2Score;
        this.expectedScore = expectedScore;
    }
    
    @Parameters
    public static Collection<Object[]> getAllScores() {
        return Arrays.asList(new Object[][] {
                { 0, 0, "Love-All" },
                { 1, 1, "Fifteen-All" },
                { 2, 2, "Thirty-All"},
                { 3, 3, "Deuce"},
                { 4, 4, "Deuce"},
                
                { 1, 0, "Fifteen-Love"},
                { 0, 1, "Love-Fifteen"},
                { 2, 0, "Thirty-Love"},
                { 0, 2, "Love-Thirty"},
                { 3, 0, "Forty-Love"},
                { 0, 3, "Love-Forty"},
                { 4, 0, "Win for player1"},
                { 0, 4, "Win for player2"},
                
                { 2, 1, "Thirty-Fifteen"},
                { 1, 2, "Fifteen-Thirty"},
                { 3, 1, "Forty-Fifteen"},
                { 1, 3, "Fifteen-Forty"},
                { 4, 1, "Win for player1"},
                { 1, 4, "Win for player2"},

                { 3, 2, "Forty-Thirty"},
                { 2, 3, "Thirty-Forty"},
                { 4, 2, "Win for player1"},
                { 2, 4, "Win for player2"},
                
                { 4, 3, "Advantage player1"},
                { 3, 4, "Advantage player2"},
                { 5, 4, "Advantage player1"},
                { 4, 5, "Advantage player2"},
                { 15, 14, "Advantage player1"},
                { 14, 15, "Advantage player2"},

                { 6, 4, "Win for player1"},
                { 4, 6, "Win for player2"},
                { 16, 14, "Win for player1"},
                { 14, 16, "Win for player2"},
        });
    }

    public void checkAllScores(TennisGame game) {
        int highestScore = Math.max(this.player1Score, this.player2Score);
        for (int i = 0; i < highestScore; i++) {
            if (i < this.player1Score)
                game.wonPoint("player1");
            if (i < this.player2Score)
                game.wonPoint("player2");
        }
        assertEquals(this.expectedScore, game.getScore());
    }

    @Test
    public void checkAllScoresTennisGame1() {
        TennisGame1 game = new TennisGame1("player1", "player2");
        checkAllScores(game);
    }

    @Test
    public void checkAllScoresTennisGame2() {
        TennisGame2 game = new TennisGame2("player1", "player2");
        checkAllScores(game);
    }

    @Test
    public void checkAllScoresTennisGame3() {
        TennisGame3 game = new TennisGame3("player1", "player2");
        checkAllScores(game);
    }

}
```
Here we create a Arrays.asList() in the following manner:
```
Arrays.asList(new Object[][] {
                { 0, 0, "Love-All" },
                { 1, 1, "Fifteen-All" },
                { 2, 2, "Thirty-All"},
                { 3, 3, "Deuce"},
                { 4, 4, "Deuce"},
                
```
This is the checkAllScores() method:
```
public void checkAllScores(TennisGame game) {
    int highestScore = Math.max(this.player1Score, this.player2Score);
    for (int i = 0; i < highestScore; i++) {
        if (i < this.player1Score)
            game.wonPoint("player1");
        if (i < this.player2Score)
            game.wonPoint("player2");
    }
    assertEquals(this.expectedScore, game.getScore());
}
```
This test takes Math.max() of this.player1Score and this.player2Score. The for loop checks if the iteration is less than the player1Score and then increases the game.wonPoint("player1"). 