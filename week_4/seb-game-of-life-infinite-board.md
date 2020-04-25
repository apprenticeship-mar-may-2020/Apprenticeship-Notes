###### Game of Life
This was Sebastian's Game of Life that he completed for the Pair programming with Ed Rixon:

This was the AcceptanceTest.java:
```
package com.codurance.game_of_life;

import org.junit.jupiter.api.Test;

import java.util.Arrays;
import java.util.List;

import static org.junit.jupiter.api.Assertions.assertFalse;
import static org.junit.jupiter.api.Assertions.assertTrue;

class AcceptanceTest {

//     O
//     ON
//    OO

    @Test
    void on_first_evolution_one_should_die_one_new_cell_rest_survive() {
        Position rootOne = new Position(0, 0);
        Position rootTwo = new Position(0, -1);
        Position rootThree = new Position(0, -2);
        Position rootFour = new Position(-1, -2);

        List<Position> worldSeed = Arrays.asList(
                rootOne,
                rootTwo,
                rootThree,
                rootFour
        );
        GameOfLife gameOfLife = new GameOfLife(worldSeed);

        List<Position> newWorld = gameOfLife.evolve();


        assertFalse(newWorld.contains(rootOne));

        assertTrue(newWorld.contains(rootTwo));
        assertTrue(newWorld.contains(rootThree));
        assertTrue(newWorld.contains(rootFour));

        assertTrue(newWorld.contains(new Position(1,-1)));
    }
}
```
//  O
//  ON
// OO

Here we set up four positions (as shown by the sketch) and tehn create a worldSeed ArrayList from the Positions. He then puts this worldSeed into the GameOfLife. the expectation of the test is that rootOne is not in the result of gameOfLife.evolve().
However the newWorld does contain rootTwo, rootThree and rootFour. The newWorld also contains a new Position(1, -1).

The GameOfLife looks like this:
```
package com.codurance.game_of_life;

import java.util.Collections;
import java.util.List;

public class GameOfLife {
    private World world;

    public GameOfLife(List<Position> worldSeed) {
        this.world = new World(worldSeed);
    }

    public List<Position> evolve() {
        world.evolve();
        return Collections.unmodifiableList(world.lifeCells());
    }
}
```
He returns an unmodifiableList of world.lifeCells so that this is imutable as it is used to put back into the GameOfLife after the evolve method.

The Cell itself has very little state on it or logic relating to the game:
```
package com.codurance.game_of_life;

public class Cell {
    public static boolean shouldCellSurvive(int aliveNeighbours) {
        return aliveNeighbours < 2 || aliveNeighbours > 3;
    }

    public static boolean shouldResurect(int aliveNeighbours) {
        return aliveNeighbours == 3;
    }
}
```
The public static boolean method returns whether the Cell should survive or not and also whether it should resurect. The Position class returns the neighbours from itself:
```
package com.codurance.game_of_life;

import java.util.*;

public class Position {
    public final int xPos;
    public final int yPos;
    private List<Position> neighbours;

    public Position(int xPos, int yPos) {
        this.xPos = xPos;
        this.yPos = yPos;
        neighbours = new ArrayList<>();
    }

    public void populateNeighbours() {
        neighbours = Arrays.asList(
                new Position(xPos-1, yPos+1),
                new Position(xPos-1, yPos),
                new Position(xPos-1, yPos-1),

                new Position(xPos, yPos+1),
                new Position(xPos, yPos-1),

                new Position(xPos+1, yPos+1),
                new Position(xPos+1, yPos),
                new Position(xPos+1, yPos-1)
        );
    }

    public List<Position> getNeighbours() {
        populateNeighbours();
        return Collections.unmodifiableList(neighbours);
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        Position position = (Position) o;
        return xPos == position.xPos &&
                yPos == position.yPos;
    }

    @Override
    public int hashCode() {
        return Objects.hash(xPos, yPos);
    }
}
```
This uses HashCode and equals to override the normal equality comparison. The populate neighbours method returns the neighbours above and below the cell. It then returns these neighbours with getNeighbours. The World is where alot of the logic for the application resides:
```
package com.codurance.game_of_life;

import java.util.*;

public class World {
    private Map<Position, Cell> lifeCells;

    public World(List<Position> worldSeed) {
        lifeCells = new HashMap<>();
        for (Position cellPosition : worldSeed) {
            lifeCells.put(cellPosition, new Cell());
        }
    }

    public List<Position> lifeCells() {
        return List.copyOf(lifeCells.keySet());
    }

    public void evolve() {
        Map<Position, Cell> newWorld = new HashMap<>(lifeCells);
        Set<Position> deadToCheck = new HashSet<>();

        for (Position cellPosition : lifeCells.keySet()) {
            List<Position> neighbours = cellPosition.getNeighbours();
            getDeadNeighbours(deadToCheck, neighbours);
            killCell(newWorld, cellPosition, countAliveNeighbours(neighbours));
        }

        for (Position deadCell : deadToCheck) {
            List<Position> neighbours = deadCell.getNeighbours();
            resurrectCell(newWorld, deadCell, countAliveNeighbours(neighbours));
        }

        lifeCells = newWorld;
    }

    private void getDeadNeighbours(Set<Position> deadToCheck, List<Position> neighbours) {
        for (Position neighbour : neighbours) {
            if (!lifeCells.containsKey(neighbour)) {
                deadToCheck.add(neighbour);
            }
        }
    }

    private int countAliveNeighbours(List<Position> neighbours) {
        int aliveNeighbours = 0;
        for (Position neighbour : neighbours) {
            if (lifeCells.containsKey(neighbour)) {
                aliveNeighbours++;
            }
        }
        return aliveNeighbours;
    }

    private void resurrectCell(Map<Position, Cell> newWorld, Position deadCell, int aliveNeighbours) {
        if (Cell.shouldResurect(aliveNeighbours)) {
            newWorld.put(deadCell, new Cell());
        }
    }

    private void killCell(Map<Position, Cell> newWorld, Position cellPosition, int aliveNeighbours) {
        if (Cell.shouldCellSurvive(aliveNeighbours)) {
            newWorld.remove(cellPosition);
        }
    }
}
```
We start with a HashMap of positions:
```
package com.codurance.game_of_life;

import java.util.*;

public class World {
    private Map<Position, Cell> lifeCells;

    public World(List<Position> worldSeed) {
        lifeCells = new HashMap<>();
        for (Position cellPosition : worldSeed) {
            lifeCells.put(cellPosition, new Cell());
        }
    }

    public List<Position> lifeCells() {
        return List.copyOf(lifeCells.keySet());
    }

```
The constructor iterate over the positions in the world seed and puts the cellPosition and new Cell. This only creates entries for the Positions in the worldSeed. The next method evolve() returns the newWorld:
```
    public void evolve() {
        Map<Position, Cell> newWorld = new HashMap<>(lifeCells);
        Set<Position> deadToCheck = new HashSet<>();

        for (Position cellPosition : lifeCells.keySet()) {
            List<Position> neighbours = cellPosition.getNeighbours();
            getDeadNeighbours(deadToCheck, neighbours);
            killCell(newWorld, cellPosition, countAliveNeighbours(neighbours));
        }

        for (Position deadCell : deadToCheck) {
            List<Position> neighbours = deadCell.getNeighbours();
            resurrectCell(newWorld, deadCell, countAliveNeighbours(neighbours));
        }

        lifeCells = newWorld;
    }

```
It creates a new Map<Position, Cell> newWorld as a HashMap with the LifeCells. It then creates a Set of Position called deadToCheck as a HashSet<>(). Sebastian then iterates over the lifecells keys and gets the neighbours for each cellPosition. It then gets the deadNeighbours and adds them to the deadToCheck list:
```
    private void getDeadNeighbours(Set<Position> deadToCheck, List<Position> neighbours) {
        for (Position neighbour : neighbours) {
            if (!lifeCells.containsKey(neighbour)) {
                deadToCheck.add(neighbour);
            }
        }
    }
```
The next forLoop in evolve:
```


        for (Position deadCell : deadToCheck) {
            List<Position> neighbours = deadCell.getNeighbours();
            resurrectCell(newWorld, deadCell, countAliveNeighbours(neighbours));
        }
```
iterates over the deadToCheck set and gets the neighbours of the deadCell and ressurrects teh neighbours by passing countAliveNeighbours(neighbours). We also have these private methods:
```

    private void getDeadNeighbours(Set<Position> deadToCheck, List<Position> neighbours) {
        for (Position neighbour : neighbours) {
            if (!lifeCells.containsKey(neighbour)) {
                deadToCheck.add(neighbour);
            }
        }
    }

    private int countAliveNeighbours(List<Position> neighbours) {
        int aliveNeighbours = 0;
        for (Position neighbour : neighbours) {
            if (lifeCells.containsKey(neighbour)) {
                aliveNeighbours++;
            }
        }
        return aliveNeighbours;
    }

    private void resurrectCell(Map<Position, Cell> newWorld, Position deadCell, int aliveNeighbours) {
        if (Cell.shouldResurect(aliveNeighbours)) {
            newWorld.put(deadCell, new Cell());
        }
    }

    private void killCell(Map<Position, Cell> newWorld, Position cellPosition, int aliveNeighbours) {
        if (Cell.shouldCellSurvive(aliveNeighbours)) {
            newWorld.remove(cellPosition);
        }
    }
```
We saw getDeadNeighbours:
```
    private void getDeadNeighbours(Set<Position> deadToCheck, List<Position> neighbours) {
        for (Position neighbour : neighbours) {
            if (!lifeCells.containsKey(neighbour)) {
                deadToCheck.add(neighbour);
            }
        }
    }

```
This method iterates over the neighbours and if the lifeCells does not contain the key neighbour then we add the nieghbour to deadToCheck. We do a similar action with countAliveNeighbours:

```
    private int countAliveNeighbours(List<Position> neighbours) {
        int aliveNeighbours = 0;
        for (Position neighbour : neighbours) {
            if (lifeCells.containsKey(neighbour)) {
                aliveNeighbours++;
            }
        }
        return aliveNeighbours;
    }
```
Finally we use resurrectCell and killCell():
```

    private void resurrectCell(Map<Position, Cell> newWorld, Position deadCell, int aliveNeighbours) {
        if (Cell.shouldResurect(aliveNeighbours)) {
            newWorld.put(deadCell, new Cell());
        }
    }

    private void killCell(Map<Position, Cell> newWorld, Position cellPosition, int aliveNeighbours) {
        if (Cell.shouldCellSurvive(aliveNeighbours)) {
            newWorld.remove(cellPosition);
        }
    }
```
The resurrectCell and killCell either put or remove the cellPosition from the HashMap. Next we use tests to check the results. The first is the Acceptance test:
```
package com.codurance.game_of_life;

import org.junit.jupiter.api.Test;

import java.util.Arrays;
import java.util.List;

import static org.junit.jupiter.api.Assertions.assertFalse;
import static org.junit.jupiter.api.Assertions.assertTrue;

class AcceptanceTest {

//     O
//     ON
//    OO

    @Test
    void on_first_evolution_one_should_die_one_new_cell_rest_survive() {
        Position rootOne = new Position(0, 0);
        Position rootTwo = new Position(0, -1);
        Position rootThree = new Position(0, -2);
        Position rootFour = new Position(-1, -2);

        List<Position> worldSeed = Arrays.asList(
                rootOne,
                rootTwo,
                rootThree,
                rootFour
        );
        GameOfLife gameOfLife = new GameOfLife(worldSeed);

        List<Position> newWorld = gameOfLife.evolve();


        assertFalse(newWorld.contains(rootOne));

        assertTrue(newWorld.contains(rootTwo));
        assertTrue(newWorld.contains(rootThree));
        assertTrue(newWorld.contains(rootFour));

        assertTrue(newWorld.contains(new Position(1,-1)));
    }
}
```
This test creates a seed of four positions and then asserts the expected positions of the cells on the newWorld after gameOfLife.evolve() has been called. This next test is concerning the GameOfLife itself:
```
package com.codurance.game_of_life;

import org.junit.jupiter.api.Test;

import java.util.Arrays;
import java.util.Collections;
import java.util.List;

import static org.junit.jupiter.api.Assertions.assertEquals;

class GameOfLifeShould {

    @Test
    void be_initialize_with_given_seed() {

        List<Position> worldSeed = Collections.EMPTY_LIST;

        GameOfLife gameOfLife = new GameOfLife(worldSeed);

        assertEquals(Collections.EMPTY_LIST, gameOfLife.evolve());
    }

    @Test
    void return_empty_world_if_only_one_cell_seed() {

        List<Position> worldSeed = Arrays.asList(
                new Position(0,0)
        );

        GameOfLife gameOfLife = new GameOfLife(worldSeed);

        assertEquals(Collections.EMPTY_LIST, gameOfLife.evolve());
    }
}
```
This test checks for evolve on an empty list and then checks gameOfLife.evolve() on a world with one cell in the seed. The next test concerns the WorldShould:
```
package com.codurance.game_of_life;

import org.junit.jupiter.api.Test;

import java.util.Arrays;
import java.util.Collections;
import java.util.List;

import static org.junit.jupiter.api.Assertions.assertEquals;
import static org.junit.jupiter.api.Assertions.assertTrue;

class WorldShould {
    @Test
    void if_one_cell_evolve_to_dead() {
        World world = new World(Arrays.asList(new Position(0,0)));

        world.evolve();

        assertEquals(Collections.EMPTY_LIST, world.lifeCells());
    }

    @Test
    void return_same_world_with_square_seed() {
        Position position1 = new Position(0, 0);
        Position position2 = new Position(0, 1);
        Position position3 = new Position(1, 1);
        Position position4 = new Position(1, 0);
        World world = new World(Arrays.asList(
                position1,
                position2,
                position3,
                position4
        ));

        world.evolve();
        List<Position> newWorld = world.lifeCells();

        assertTrue(newWorld.contains(position1));
        assertTrue(newWorld.contains(position2));
        assertTrue(newWorld.contains(position3));
        assertTrue(newWorld.contains(position4));
    }
}
```
This test checks for returning the same world with a square seed. If the world is an exact 4 x 4 square then it stays the same.

