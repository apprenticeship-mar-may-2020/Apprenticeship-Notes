##### Mars Rover state pattern:
Sebastian and I worked on the State Pattern for the Mars Rover:
```
package com.codurance.game_of_life;

import com.codurance.game_of_life.states.State;

public class Cell {

  private State state;

  public Cell(State state) {
    this.state = state;
  }

  public boolean isAlive() {
    return state.isAlive();
  }

  public void evolve(int liveNeighbours) {
    state.evolve(liveNeighbours, this);
  }

  public void changeState(State state){
    this.state = state;
  }
}
```
We started with the Cell and initialized it with a State (Alive or Dead). These responded individually to the isAlive() function. We then used the evolve method to call evolve on the state passing liveNeighbours count and the Rover's context to the evolve function on State:
```
package com.codurance.game_of_life.states;

import com.codurance.game_of_life.Cell;

public class Alive implements State {

  @Override
  public final boolean isAlive() {
    return true;
  }

  @Override
  public void evolve(int liveNeighbours, Cell cell) {
    if (liveNeighbours < 2 || liveNeighbours > 3){
      cell.changeState(new Dead());
    }
  }
}s
```
The evolve method then calls changeState on the cell which means that we then change the state on the cell to dead if there are less than 2 or more than 3 liveNeighbours. We then started to implement the Position class in preparation for creating the board (which would be a HashMap):
```
package com.codurance.game_of_life;

import java.util.ArrayList;
import java.util.List;
import java.util.Objects;

public class Position {
    private final int x;
    private final int y;

    public Position(int x, int y) {
        this.x = x;
        this.y = y;
    }


    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        Position position = (Position) o;
        return x == position.x &&
                y == position.y;
    }

    @Override
    public int hashCode() {
        return Objects.hash(x, y);
    }

    public List<Position> getNeighbours() {
        ArrayList<Position> neighbours = new ArrayList<>();
        for (int i = x - 1; i <= x + 1; i++) {
            for (int j = y - 1; j <= y + 1; j++) {
                if (i == x && j == y) {
                    continue;
                }
                neighbours.add(new Position(i, j));
            }
        }
        return neighbours;
    }
}
```
For this we needed to override the equals method so that we could directly access the Position from the hash and also we created an array of neighbours for the Position with a nested for loop. 