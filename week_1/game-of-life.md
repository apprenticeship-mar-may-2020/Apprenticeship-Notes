#### Game of Life
This video is part of a 29 part video series on the Game of Life:
#### This is the playlist:
https://www.youtube.com/playlist?list=PLwAX_Bwbts_fI71bo62NwIMc05-uGigza

https://www.youtube.com/watch?v=2oa0U53B278


#### This is a useful (and probably the best) video on Game of Life:
https://www.youtube.com/watch?v=vl9Ndams4L8&list=PLyrmM-bwmbZGx-YdhUOB3Nk_rUXgD2x-r

##### This is how we started:

```
import Board.Board;
import GameOfLife.GameOfLife;
import org.junit.Test;

import static org.junit.jupiter.api.Assertions.assertArrayEquals;
import static org.junit.jupiter.api.Assertions.assertEquals;

public class GameOfLifeShould {
  @Test
  public void live_cell_with_zero_live_neighbours_dies() {

    int[][] array = {{0, 0, 0},{0, 1, 0},{0, 0, 0}};
    Board start = new Board(array);

    GameOfLife gameOfLife = new GameOfLife();

    assertEquals(0, gameOfLife.nextGen(start).getBoard()[1][1]);
  }

  @Test
  public void live_cell_with_one_live_neighbours_dies() {

    int[][] array = {{0, 0, 0},{0, 1, 1},{0, 0, 0}};
    Board start = new Board(array);

    GameOfLife gameOfLife = new GameOfLife();

    assertEquals(0, gameOfLife.nextGen(start).getBoard()[1][1]);
  }

  @Test
  public void live_cell_with_two_live_neighbours_stays_alive() {


    int[][] array = {{0, 0, 0},{1, 1, 1},{0, 0, 0}};
    Board start = new Board(array);

    GameOfLife gameOfLife = new GameOfLife();

    assertEquals(1, gameOfLife.nextGen(start).getBoard()[1][1]);
  }

  @Test
  public void live_cell_with_three_live_neighbours_stays_alive() {


    int[][] array = {{0, 1, 0},{1, 1, 1},{0, 0, 0}};
    Board start = new Board(array);

    GameOfLife gameOfLife = new GameOfLife();

    assertEquals(1, gameOfLife.nextGen(start).getBoard()[1][1]);
  }
}

```
We had tests for single inner array:

```
package GameOfLife;

import Board.Board;

public class GameOfLife {

  private Board board;

  public Board nextGen(Board board) {
    this.board = board;

    int[][] nextGenArray = nextArray(board);
    return new Board(nextGenArray);
  }

  private int[][] nextArray(Board board){
    int livingNeighbours = getNeighbours(1, 1);
    int[][] nextArray = board.getBoard();

    if (livingNeighbours < 2) {
      nextArray[1][1] = 0;
    }
    return nextArray;
  }

  private int getNeighbours(int x, int y) {
    return board.getBoard()[x][y - 1] + board.getBoard()[x][y + 1];
  }
}
```

This was the board:
```
package Board;

import org.apache.commons.lang3.builder.EqualsBuilder;
import org.apache.commons.lang3.builder.HashCodeBuilder;

public class Board {
  private final int[][] board;

  public Board(int[][] array){
    this.board = array;
  }

  public int[][] getBoard() {
    return board;
  }



  @Override
  public boolean equals(Object o) {
    if (this == o) return true;

    if (o == null || getClass() != o.getClass()) return false;

    Board board1 = (Board) o;

    return new EqualsBuilder()
            .append(board, board1.board)
            .isEquals();
  }
}
```

This is changing the methods to ternaries:
```
package GameOfLife;

import Board.Board;

public class GameOfLife {

  private Board board;

  public Board nextGen(Board board) {
    this.board = board;

    int[][] nextGenArray = nextArray(board);
    return new Board(nextGenArray);
  }

  private int[][] nextArray(Board board){
    int livingNeighbours = getNeighbours(1, 1);
    int[][] nextArray = board.getBoard();

    nextArray[1][1] = nextArray[1][1] == 1 ? tickLivingCell(livingNeighbours) : tickDeadCell(livingNeighbours);

    return nextArray;
  }

  private int tickDeadCell(int livingNeighbours) {
    return livingNeighbours == 3 ? 1 : 0;
  }

  private int tickLivingCell(int livingNeighbours) {
    return livingNeighbours >= 2 && livingNeighbours <= 3 ? 1 : 0;
  }

  private int getNeighbours(int x, int y) {
    return board.getBoard()[x][y - 1] +
            board.getBoard()[x][y + 1] +
            board.getBoard()[x + 1][y] +
            board.getBoard()[x - 1][y];
  }
}

```

This was the comment from Sherlock:
```
...implement your own method which will take the initial state as an NxM array of 0's (dead 
cell) and 1's (living cell) and return an equally sized array 
representing the next generation. Cells outside the array must be 
considered dead. Cells that would born out of the array boundaries 
should be ignored (universe never grows beyond the initial NxM grid).
```

###### Alexis and Sebastian used the logic for the Game of life in the Cell:

```
package com.codurance.game_of_life;

public class Cell {

    private boolean alive;

    public Cell(boolean alive) {
        this.alive = alive;
    }

    public void mutate(int aliveNeighbours) {
        alive = !resurection(aliveNeighbours) && shouldSurvive(aliveNeighbours);
    }

    private boolean shouldSurvive(int aliveNeighbours) {
        return aliveNeighbours >= 2 && aliveNeighbours < 4;
    }

    private boolean resurection(int aliveNeighbours) {
        return aliveNeighbours == 2 && !alive;
    }


    public boolean isAlive() {
        return alive;
    }

    public String printState() {
        return alive ? "O" : "X";
    }
}
```
Here the cell is deciding whether it should live or not. The Game of Life method on the other hand just takes a board and then reassigns it each time:
```
package com.codurance.game_of_life;

public class GameOfLife {

    private Board currentBoard;



    public GameOfLife(Board board) {
        currentBoard = board;
    }

    public String printBoard() {
        return currentBoard.print();
    }

}
```
The integer position for the Game of Life cells is in its own class:
```
package com.codurance.game_of_life;

public class Position {
    public final int x;
    public final int y;

    public Position(int x, int y) {
        this.x = x;
        this.y = y;
    }
}
```
The Board is responsible for the rows columns and printing itself:
```
package com.codurance.game_of_life;

import java.util.List;

public class Board {
    private Cell[][] cells;

    public Board(int x, int y) {
        cells = new Cell[x][y];
        for (int i = 0; i < cells.length; i++) {
            for (int j = 0; j < cells[i].length; j++) {
                cells[i][j] = new Cell(false);
            }
        }
    }

    public Board(int x, int y, List<Position> aliveCells) {
        this(x,y);
        for (Position aliveCell : aliveCells) {
            cells[aliveCell.x][aliveCell.y] = new Cell(true);
        }
    }


    public String print(){
        return makeRows().toString();
    }

    private StringBuilder makeRows() {
        StringBuilder stringBuilder = new StringBuilder();
        for (int i = 0; i < cells.length; i++) {
            makeColumns(stringBuilder, cells[i]);
        }
        return stringBuilder;
    }

    private StringBuilder makeColumns(StringBuilder stringBuilder, Cell[] cells) {
        for (int j = 0; j < cells.length; j++) {
            stringBuilder.append(cells[j].printState());
        }
        stringBuilder.append("\n");
        return stringBuilder;
    }
}
```

##### The tests for this are:
```
package com.codurance.game_of_life;

import org.junit.jupiter.api.Test;

import java.util.Arrays;

import static org.junit.jupiter.api.Assertions.assertEquals;

class GameOfLifeShould {


    @Test
    public void create_game_on_5_5_grid() {
        GameOfLife gameOfLife = new GameOfLife(new Board(5, 5));
        String expectedGrid = "XXXXX\n"
            + "XXXXX\n"
            + "XXXXX\n"
            + "XXXXX\n"
            + "XXXXX\n";
        assertEquals(expectedGrid, gameOfLife.printBoard());
    }

    @Test
    public void create_game_on_6_6_grid() {
        GameOfLife gameOfLife = new GameOfLife(new Board(6, 6));
        String expectedGrid = "XXXXXX\n"
            + "XXXXXX\n"
            + "XXXXXX\n"
            + "XXXXXX\n"
            + "XXXXXX\n"
            + "XXXXXX\n";
        assertEquals(expectedGrid, gameOfLife.printBoard());
    }

    @Test
    public void create_game_on_7_7_grid() {
        GameOfLife gameOfLife = new GameOfLife(new Board(7, 7));
        String expectedGrid = "XXXXXXX\n"
            + "XXXXXXX\n"
            + "XXXXXXX\n"
            + "XXXXXXX\n"
            + "XXXXXXX\n"
            + "XXXXXXX\n"
            + "XXXXXXX\n";
        assertEquals(expectedGrid, gameOfLife.printBoard());
    }

    @Test
    void create_game_with_alive_cell_on_5_5_grid() {
        GameOfLife gameOfLife = new GameOfLife(new Board(5, 5, Arrays.asList(new Position(2,2))));
        String expectedGrid = "XXXXX\n"
                + "XXXXX\n"
                + "XXOXX\n"
                + "XXXXX\n"
                + "XXXXX\n";
        assertEquals(expectedGrid, gameOfLife.printBoard());
    }
}
```
These tests check that we are creating a new grid each time with varying sizes. The final test checks for creating a grid with an array including an instance of the Position class. The tests for cell are quite simple in that they just fulfill the rules of the Game of Life:
```
package com.codurance.game_of_life;

import static org.junit.jupiter.api.Assertions.assertFalse;
import static org.junit.jupiter.api.Assertions.assertTrue;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;

public class CellShould {

    private Cell aliveCell;

    @BeforeEach
    void setUp() {
        aliveCell = new Cell(true);
    }

    @Test
    public void should_die_if_1_alive() {
        aliveCell.mutate(1);
        assertFalse(aliveCell.isAlive());
    }

    @Test
    public void should_die_if_0_alive() {
        aliveCell.mutate(0);
        assertFalse(aliveCell.isAlive());
    }

    @Test
    public void should_survive_on_2_alive() {
        aliveCell.mutate(2);
        assertTrue(aliveCell.isAlive());
    }

    @Test
    public void should_survive_on_3_alive() {
        aliveCell.mutate(3);
        assertTrue(aliveCell.isAlive());
    }

    @Test
    public void should_die_on_4_alive() {
        aliveCell.mutate(4);
        assertFalse(aliveCell.isAlive());
    }

    @Test
    public void should_die_on_7_alive() {
        aliveCell.mutate(7);
        assertFalse(aliveCell.isAlive());
    }

    @Test
    public void should_become_alive_on_3_alive() {
        Cell deadCell = new Cell(false);
        deadCell.mutate(3);
        assertTrue(deadCell.isAlive());
    }

    @Test
    public void should_stay_dead_on_2_alive() {
        Cell deadCell = new Cell(false);
        deadCell.mutate(2);
        assertFalse(deadCell.isAlive());
    }

}
```

##### Tick game of life:
This was the explanation of the term tick:
```
The initial pattern constitutes the 'seed' of the system.
The first generation is created by applying the above 
rules simultaneously to every cell in the seed — births and 
deaths happen simultaneously, and the discrete moment 
at which this happens is sometimes called a tick.
```
I then realised that this term also came up in the Gilded Rose Kata to describe the changes on items in the Gilded Rose shop.

##### Sherlock - UML diagram:
Sherlock shared this diagram on game of life:

![Screenshot 2020-03-05 at 17 19 05](https://user-images.githubusercontent.com/27693622/76010489-e258bf00-5f0a-11ea-8581-bff18f6e8d1f.png)

This shows the planning process that needs to go into the creation of a Cell, World, Survive Rules, World Specification, Game of Life game. 

This is another diagram:
![Screenshot 2020-03-05 at 17 17 52](https://user-images.githubusercontent.com/27693622/76010682-36fc3a00-5f0b-11ea-91e0-e54623111a6f.png)

This includes GameOfLife, Position and World and is based on outside in testing for the API.

![Screenshot 2020-03-05 at 10 54 55](https://user-images.githubusercontent.com/27693622/76010780-6317bb00-5f0b-11ea-9e3e-5f1c4ff014de.png)

Here the GameOfLife(int x, int y) uses a grid, printGrid() method uses StringBuilder(). Alexis had a nested For loop which then appended the latest X to the StringBuilder to create the board.

This was the test for PrintGrid():
![Screenshot 2020-03-05 at 10 48 13](https://user-images.githubusercontent.com/27693622/76011026-c99cd900-5f0b-11ea-8c71-18bbde5c3bd3.png)

The tests checked for 5_5, 6_6 and 7_7 grids.

This was the list of methods used in Cell by one of the versions that Alexis and Sebastian made:

![Screenshot 2020-03-05 at 10 49 01](https://user-images.githubusercontent.com/27693622/76020545-8480a300-5f1b-11ea-8a11-3a5c171f6131.png)




###### Ewan and my solution for Java Game of Life was the following:
```
```


#### This Ruby implementation used a lot of code in the Grid class:
```
require_relative 'cell'

class Grid
  attr_reader :size

  def initialize(size, state: nil)
    @size = size
    if state
      @storage = state.map { |status| Cell.new(status) }
    else
      @storage = number_of_cells(@size).times.map { Cell.new }
    end
  end

  def get_neighbors_from_index(index)
    indices_to_retrieve = []
    indices_to_retrieve << (index - 1) unless beginning_of_row?(index)
    indices_to_retrieve << (index + 1) unless end_of_row?(index)

    unless first_row?(index)
      indices_to_retrieve << (above(index - 1)) unless beginning_of_row?(index)
      indices_to_retrieve << (above(index + 1)) unless end_of_row?(index)
      indices_to_retrieve << (above(index))
    end

    unless last_row?(index)
      indices_to_retrieve << (below(index) - 1) unless beginning_of_row?(index)
      indices_to_retrieve << (below(index) + 1) unless end_of_row?(index)
      indices_to_retrieve << (below(index))
    end

    indices_to_retrieve.map { |i| @storage[i] }
  end

  def [](x, y)
    @storage[x + (y * size)]
  end

  def generate!
    next_generation = {}

    @storage.each_with_index do |cell, index|
      neighbors = get_neighbors_from_index(index)
      living_neighbor_count = neighbors.select(&:alive?).count

      if cell.alive? && [2, 3].include?(living_neighbor_count)
        next_generation[index] = :alive
      elsif cell.alive? && living_neighbor_count == 4
        next_generation[index] = :dead
      elsif cell.dead? && living_neighbor_count == 3
        next_generation[index] = :alive
      else
        next_generation[index] = :dead
      end
    end

    next_generation.each do |index, status|
      status == :alive ? @storage[index].live! : @storage[index].die!
    end
  end

  def state
    @storage.map(&:status)
  end

  def to_s
    grid_string = ""

    @storage.each_with_index do |cell, index|
      grid_string += " #{cell}"
      grid_string += "\n" if end_of_row?(index)
    end

    grid_string
  end

  private
  def beginning_of_row?(index)
    index % size == 0
  end

  def end_of_row?(index)
    (index + 1) % size == 0
  end

  def first_row?(index)
    index < size
  end

  def last_row?(index)
    index >= (number_of_cells(size) - size)
  end

  def size_matches_state?(size, state)
    number_of_cells(size) == state.length
  end

  def above(index)
    index - size
  end

  def below(index)
    index + size
  end

  def number_of_cells(size)
    size * size
  end
end
```
