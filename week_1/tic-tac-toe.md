##### Tic Tac Toe
This is Richard Wild's functional approach to tic tac toe:
https://codurance.com/videos/2018-03-29-tic-tac-toe-functional-speedrun/

This was Sandro's solution:
https://github.com/sandromancuso/tic-tac-toe/tree/master/java/src/test/java/org/craftedsw/tictactoe


```
public class TicTacToe {
    private char[][] board;
    private char currentPlayerMark;
    public TicTacToe() {
        board = new char[3][3];
        currentPlayerMark = 'x';
        initializeBoard();
    }
    // Set/Reset the board back to all empty values.
    public void initializeBoard() {
        // Loop through rows
        for (int i = 0; i < 3; i++) {
            // Loop through columns
            for (int j = 0; j < 3; j++) {
                board[i][j] = '-';
            }
        }
    }
    // Print the current board (may be replaced by GUI implementation later)
    public void printBoard() {
        System.out.println("-------------");
        for (int i = 0; i < 3; i++) {
            System.out.print("| ");
            for (int j = 0; j < 3; j++) {
                System.out.print(board[i][j] + " | ");
            }
            System.out.println();
            System.out.println("-------------");
        }
    }
    // Loop through all cells of the board and if one is found to be empty (contains char '-') then return false.
    // Otherwise the board is full.
    public boolean isBoardFull() {
        boolean isFull = true;
        for (int i = 0; i < 3; i++) {
            for (int j = 0; j < 3; j++) {
                if (board[i][j] == '-') {
                    isFull = false;
                }
            }
        }
        return isFull;
    }
    // Returns true if there is a win, false otherwise.
    // This calls our other win check functions to check the entire board.
    public boolean checkForWin() {
        return (checkRowsForWin() || checkColumnsForWin() || checkDiagonalsForWin());
    }
    // Loop through rows and see if any are winners.
    private boolean checkRowsForWin() {
        for (int i = 0; i < 3; i++) {
            if (checkRowCol(board[i][0], board[i][1], board[i][2]) == true) {
                return true;
            }
        }
        return false;
    }
    // Loop through columns and see if any are winners.
    private boolean checkColumnsForWin() {
        for (int i = 0; i < 3; i++) {
            if (checkRowCol(board[0][i], board[1][i], board[2][i]) == true) {
                return true;
            }
        }
        return false;
    }
    // Check the two diagonals to see if either is a win. Return true if either wins.
    private boolean checkDiagonalsForWin() {
        return ((checkRowCol(board[0][0], board[1][1], board[2][2]) == true) || (checkRowCol(board[0][2], board[1][1], board[2][0]) == true));
    }
    // Check to see if all three values are the same (and not empty) indicating a win.
    private boolean checkRowCol(char c1, char c2, char c3) {
        return ((c1 != '-') && (c1 == c2) && (c2 == c3));
    }
    // Change player marks back and forth.
    public void changePlayer() {

        if (currentPlayerMark == 'x') {
            currentPlayerMark = 'o';
        }
        else {
            currentPlayerMark = 'x';
        }
    }

    // Places a mark at the cell specified by row and col with the mark of the current player.

    public boolean placeMark(int row, int col) {
        // Make sure that row and column are in bounds of the board.
        if ((row >= 0) && (row < 3)) {
            if ((col >= 0) && (col < 3)) {
                if (board[row][col] == '-') {
                    board[row][col] = currentPlayerMark;
                    return true;
                }
            }
        }
    return false;
  }
}
```

##### This is the meaning of Object calisthenics:
```
Only one level of indentation per method
Don’t use the ELSE keyword
Wrap all primitives and strings
First class collections (wrap all collections)
Only one dot per line dog.Body.Tail.Wag() => dog.ExpressHappiness()
No abbreviations
Keep all entities small
[10 files per package, 50 lines per class, 5 lines per method, 2 arguments per method]
No classes with more than two instance variables
No public getters/setters/properties
```

#### This is another Tic Tac Toe video:
https://www.youtube.com/watch?v=_BDXk5mkxzQ

![Screenshot 2020-03-01 at 09 03 52](https://user-images.githubusercontent.com/27693622/75622743-a4bb0580-5b9b-11ea-8639-79a3ecd19d1c.png)


##### This is the Tic tac toe website:

https://github.com/jpwenzelc/tic-tac-toe

Tic Tac Toe
This is a simple game of tic tac toe, the rules are simple, given a grid of 3 by 3 spaces, players place their token in free spaces trying to make a staight line to win

The Rules
X always plays first
Players can not place their token on a position that already has a token
Players alternate placing tokens until either of this happens:
One player has three in a row (horizontally, vertically or diagonally)
All positions are filled
A player that's able to draw three tokens in a row wins
If all positions are filled an no player has three in a line that is consider a draw
Winning conditions
X . . 
X . .
X . .
X X X 
. . .
. . .
X . . 
. X .
. . X
(Please consider all variants of this)

Input/Output
Input Tictactoe.play(/* params */)

Output Current state of the board

X.. 
.O.
..X
or Player X wins


We can use the .contains method to check if a string contains a character:
```
.contains("-")
```
we can't do this on a string.

We can find numeric value of Characters here:

```
public class JavaCharactergetNumericValueExample1 {  
    public static void main(String[] args) {  
        // Initialize a codepoint  
        int codepoint = 110;  
        // Convert codepoint to char  
        char ch = (char) codepoint;  
        // Convert the code point to its numeric value  
        int digit = Character.getNumericValue(codepoint);  
        // Print the result  
System.err.println("The character '" + ch + "' has a numeric value represented as: " + digit);  
    }  
}  
```
but not for non letter chars.

This was the tic tac toe game:

![Screenshot 2020-03-04 at 11 03 58](https://user-images.githubusercontent.com/27693622/75880340-fcb07100-5e14-11ea-9407-92f170eb1d15.png)


![Screenshot 2020-03-04 at 11 01 00](https://user-images.githubusercontent.com/27693622/75880368-0934c980-5e15-11ea-86f3-ac806172dd13.png)

![Screenshot 2020-03-04 at 11 02 46](https://user-images.githubusercontent.com/27693622/75880717-b871a080-5e15-11ea-86c1-f100b10b30a3.png)

![Screenshot 2020-03-04 at 11 00 12](https://user-images.githubusercontent.com/27693622/75880745-c1fb0880-5e15-11ea-957f-8e93ca2f3ff6.png)

This solution uses a StringBuilder():


![Screenshot 2020-03-04 at 10 50 45](https://user-images.githubusercontent.com/27693622/75880789-d63f0580-5e15-11ea-8248-80502454c6d2.png)

This method uses string return for each method:

![Screenshot 2020-03-04 at 10 49 01](https://user-images.githubusercontent.com/27693622/75880809-e22ac780-5e15-11ea-83b6-fb3265a4112b.png)


##### Sherlock left this note on Tic Tac Toe:
```
Input:
String output1 = ticTacToe.play(/*params*/) // X plays
String output2 = ticTacToe.play(/*params*/) // O plays
String output3 = ticTacToe.play(/*params*/) // X plays
...
Output:
X Wins
O Wins
Draw
Ongoing/ Or anything
```

This was the description of the tic tac toe kata:

![Screenshot 2020-03-04 at 15 27 44](https://user-images.githubusercontent.com/27693622/75894892-cd0d6300-5e2c-11ea-9af3-e2966bef74bc.png)

This was Ewan and Sebastian's solution:

![Screenshot 2020-03-04 at 13 38 14](https://user-images.githubusercontent.com/27693622/75894920-d4cd0780-5e2c-11ea-90a7-f9c167726ed8.png)


![Screenshot 2020-03-04 at 13 39 10](https://user-images.githubusercontent.com/27693622/75894984-e8786e00-5e2c-11ea-98d2-61677bc3084b.png)


This was their solution continued:

![Screenshot 2020-03-04 at 13 37 25](https://user-images.githubusercontent.com/27693622/75895017-f4fcc680-5e2c-11ea-9f27-14cb07d88ab5.png)


This was the object calisthenics work from Ewan and Sebastian:
![Screenshot 2020-03-04 at 16 47 20](https://user-images.githubusercontent.com/27693622/75906300-65601380-5e3e-11ea-93fb-675d5fc66773.png)

![Screenshot 2020-03-04 at 16 44 37](https://user-images.githubusercontent.com/27693622/75906364-7e68c480-5e3e-11ea-82e6-4ce9841481b6.png)

![Screenshot 2020-03-04 at 16 45 34](https://user-images.githubusercontent.com/27693622/75906390-8aed1d00-5e3e-11ea-8ae1-9c149ed05480.png)

This is the TicTacToe Test:

![Screenshot 2020-03-04 at 16 41 45](https://user-images.githubusercontent.com/27693622/75906411-950f1b80-5e3e-11ea-916d-792b638368fe.png)

This is the RoundCounter:

![Screenshot 2020-03-04 at 16 42 46](https://user-images.githubusercontent.com/27693622/75906455-a8ba8200-5e3e-11ea-82ff-73567d03956c.png)

![Screenshot 2020-03-04 at 16 41 28](https://user-images.githubusercontent.com/27693622/75906535-c687e700-5e3e-11ea-8b17-fd38c10092cb.png)


##### This is Sandro Mancuso's marks builder:
https://github.com/sandromancuso/tic-tac-toe/blob/master/java/src/test/java/org/craftedsw/tictactoe/builder/MarksBuilder.java


##### This was Arnaud's summary of DDD:
https://github.com/aclaudel/ddd-book-summary

This is the video on the shopping cart kata in Java:
https://www.youtube.com/watch?v=kBNThogwWYw

##### This is the exit criteria for the course:
- Has to have experience in a Codurance client and/or working in a Codurance team
- Be able to architect and advise on simple distributed systems
- Be able to engineer and advise on simple distributed systems
- Be able to implement and advise on simple (Web App, Web API) (no cloud/no containers) full CI/CD pipelines
- Be a consultant
- Able to interact fluently with all client roles technical and non-technical
- Able to give options to solve problems
- Able to coach other developers
- Able to spot business opportunities (Too much? To be discussed)
- Be confident with corporate politics (Too much? To be discussed)
- Be able to express ideas clearly
- Practice talks, workshops, mentor