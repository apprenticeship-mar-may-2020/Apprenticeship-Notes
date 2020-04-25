##### Mars Rover
https://www.youtube.com/watch?v=24vzFAvOzo0

This was my implementation for the Mars Rover

The Rover had an implement method and a Grid:

```
package com.codurance.mars_rover;

import java.util.Optional;

import static com.codurance.mars_rover.Direction.*;

public class Rover {
  private Grid grid;

  Direction direction = NORTH;
  Coordinate coordinate = new Coordinate(0, 0);

  public Rover(Grid grid) {
    this.grid = grid;
  }

  public String execute(String commands) {
    String obstacleString = "";

    for (char c : commands.toCharArray()){
      if (c == 'R'){
        direction = direction.right();
      }
      if (c == 'L') {
        direction = direction.left();
      }

      if (c == 'M'){
        obstacleString = move();
      }
    }

    return obstacleString + coordinate.x() + ":" + coordinate.y() + ":" + direction.value();
  }

  private String move() {
    String obstacleString;
    Optional<Coordinate> nextCoordinate = grid.nextCoordinateFor(this.coordinate, direction);
    nextCoordinate.ifPresent(nc -> this.coordinate = nc);
    obstacleString = nextCoordinate.isPresent() ? "" : "O:";
    return obstacleString;
  }
}

```

We then called execute which returned the position an obstacle report. The move() method returned an obstacle string of "" or "0:" depending on whether the grid.nextCoordinateFor() returned a Coordinate of Optional.empty(). The Grid had a nextCoordinateFor method:

```
package com.codurance.mars_rover;

import java.util.Collections;
import java.util.List;
import java.util.Optional;

import static com.codurance.mars_rover.Direction.*;

public class Grid {
  private static final int MAX_WIDTH = 10;
  private static final int MAX_HEIGHT = 10;
  private List<Coordinate> obstacles = Collections.emptyList();

  public Grid() {
  }

  public Grid(List<Coordinate> obstacles) {
    this.obstacles = obstacles;
  }

  Optional<Coordinate> nextCoordinateFor(Coordinate coordinate, Direction direction) {
    int y = coordinate.y();
    int x = coordinate.x();
    if (direction == NORTH) {
      y = (y + 1) % MAX_HEIGHT;
    }

    if (direction == EAST) {
      x = (x + 1) % MAX_WIDTH;
    }

    if (direction == SOUTH) {
      y = (y > 0) ? y - 1 : MAX_HEIGHT - 1;
    }

    if (direction == WEST) {
      x = (x > 0) ? x - 1 : MAX_WIDTH - 1;
    }

    Coordinate newCoordinate = new Coordinate(x, y);

    return obstacles.contains(newCoordinate) ? Optional.empty() : Optional.of(newCoordinate);
  }
}

```
which returned Optional.empty() if the obstacles array on the Grid contained the new Coordinate.

The RoverShould test contained parameterized tests for the direction and then the moving wrapping and then finally for the obstacles:
```
package com.codurance.mars_rover;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.params.ParameterizedTest;
import org.junit.jupiter.params.provider.CsvSource;

import java.util.Arrays;

import static org.junit.jupiter.api.Assertions.assertEquals;

public class RoverShould {

  private Rover rover;

  @BeforeEach
  void setUp() {
    Grid grid = new Grid();
    rover = new Rover(grid);
  }

  @ParameterizedTest
  @CsvSource({
          "R, 0:0:E",
          "RR, 0:0:S",
          "RRR, 0:0:W",
          "RRRR, 0:0:N"
  })
  void rotate_right(String commands, String position) {
    assertEquals(position, rover.execute(commands));
  }

  @ParameterizedTest
  @CsvSource({
          "L, 0:0:W",
          "LL, 0:0:S",
          "LLL, 0:0:E",
          "LLLL, 0:0:N"
  })

  void rotate_left(String commands, String position) {
    assertEquals(position, rover.execute(commands));
  }

  @ParameterizedTest
  @CsvSource({
          "M, 0:1:N",
          "MMM, 0:3:N"
  })
  void move_up(String commands, String position) {
    assertEquals(position, rover.execute(commands));
  }

  @ParameterizedTest
  @CsvSource({
          "MMMMMMMMMM, 0:0:N",
          "MMMMMMMMMMMMMMM, 0:5:N"
  })
  void wrap_from_top_to_bottom_when_moving_north(String commands, String position) {
    assertEquals(position, rover.execute(commands));
  }

  @ParameterizedTest
  @CsvSource({
          "RM, 1:0:E",
          "RMMMMM, 5:0:E"
  })
  void move_right(String commands, String position) {
    assertEquals(position, rover.execute(commands));
  }

  @ParameterizedTest
  @CsvSource({
          "RMMMMMMMMMM, 0:0:E",
          "RMMMMMMMMMMMMMMM, 5:0:E",

  })
  void wrap_from_right_to_left_when_moving_east(String commands, String position) {
    assertEquals(position, rover.execute(commands));
  }

  @ParameterizedTest
  @CsvSource({
          "LM, 9:0:W",
          "LMMMMM, 5:0:W"
  })
  void move_left(String commands, String position) {
    assertEquals(position, rover.execute(commands));
  }

  @ParameterizedTest
  @CsvSource({
          "LLM, 0:9:S",
          "LLMMMMM, 0:5:S"
  })
  void move_south(String commands, String position) {
    assertEquals(position, rover.execute(commands));
  }

  @ParameterizedTest
  @CsvSource({
          "LLMMMMMMMMMM, 0:0:S",
          "LLMMMMMMMMMMMMMMM, 0:5:S"
  })
  void move_from_bottom_to_top_when_moving_south(String commands, String position) {
    assertEquals(position, rover.execute(commands));
  }

  @ParameterizedTest
  @CsvSource({
          "MMMM, O:0:3:N",
          "RMM, O:1:0:E"
  })
  void stop_at_obstacle(String commands, String position) {
    Coordinate obstacle_0_4 = new Coordinate(0, 4);
    Coordinate obstacle_2_0 = new Coordinate(2, 0);

    Grid grid = new Grid(Arrays.asList(obstacle_0_4, obstacle_2_0));
    Rover rover = new Rover(grid);

    assertEquals(position, rover.execute(commands));
  }
}

```

The coordinate class contained the HashCode and equals deluxe in order to allow for comparing Coordinates in the obstacles array on Grid:
```
package com.codurance.mars_rover;

import java.util.Objects;

class Coordinate {
  private int x = 0;
  private int y = 0;

  Coordinate(int x, int y) {
    this.x = x;
    this.y = y;
  }

  public int x() {
    return x;
  }

  public int y() {
    return y;
  }


  @Override
  public int hashCode() {
    return Objects.hash(x, y);
  }

  @Override
  public boolean equals(Object obj) {
    if (this == obj) {
      return true;
    }
    if (obj == null || getClass() != obj.getClass()) {
      return false;
    }
    final Coordinate other = (Coordinate) obj;
    return Objects.equals(this.x, other.x)
            && Objects.equals(this.y, other.y);
  }
}

```

The Direction enum returned the correct direction:
```
package com.codurance.mars_rover;

enum Direction {
  NORTH("N", "W", "E"),
  EAST("E", "N", "S"),
  SOUTH("S", "E", "W"),
  WEST("W", "S", "N");

  private final String value;
  private final String left;
  private final String right;

  Direction(String value, String left, String right) {
    this.value = value;
    this.left = left;
    this.right = right;
  }

  public Direction left() {
    return directionToThe(this.left);
  }

  public Direction right() {
    return directionToThe(this.right);
  }

  private Direction directionToThe(String nextDirection) {
    for (Direction direction : values()) {
      if (direction.value == nextDirection) {
        return direction;
      }
    }
    return null;
  }


  public String value() {
    return value;
  }
}
```
Here the directionToThe method returned the nextDirection if the direction.value == nextDirection parameter.